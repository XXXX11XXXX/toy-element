<script setup lang="ts">
import type { IconProps } from "./types"
import {FontAwesomeIcon} from "@fortawesome/vue-fontawesome"
import { omit } from "lodash-es"//用于忽略对象中的一些属性
import {computed} from "vue";
defineOptions({
    name:"ErIcon",
    inheritAttrs:false
})
const props=defineProps<IconProps>();
const filterProps=computed(()=>omit(props,['type','color']));//把自定义的内容先删去
const customStyles=computed(()=>({color:props.color??void 0}))
</script>
<template>
<i
    class="er-icon"
    :class="{ [`er-icon--${type}`]: type }"
    :style="customStyles"
    v-bind="$attrs"

>
<!-- 用模板字符串来实现一些动态的控制 -->
    <font-awesome-icon v-bind="filterProps" />
</i>
</template>
<style scoped>
    .er-icon{
        --er-icon-color:inherit;
        display: inline-flex;
        justify-content: center;
        position: relative;
        fill: currentColor;
        color: var(--er-icon-color);
        font-size: inherit;
    }
    @each $val in primary, info, success, warning, danger {
  .er-icon--$(val) {
    --er-icon-color: var(--er-color-$(val));
  }
}
</style>