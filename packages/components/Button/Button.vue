<script setup lang="ts">
import type { ButtonProps,ButtonEmits,ButtonInstance } from './types';
import {ref,computed,inject} from 'vue'
import {throttle} from 'lodash-es'
import ErIcon  from '../Icon/Icon.vue';
import {BUTTON_GROUP_CTX_KEY} from './constants'
defineOptions({
    name:'ErButton'
})
const props=withDefaults(defineProps<ButtonProps>(),{
    tag:"button",
    nativeType:"button",
    useThrottle:true,
    throttleDuration:500
});
const emits=defineEmits<ButtonEmits>();
const slots=defineSlots();
const iconStyle= computed(()=>({marginRight:slots.default?"6px":"0px"}))//根据是否有插槽判断是否需要间隔
const _ref=ref<HTMLButtonElement>();
const ctx=inject(BUTTON_GROUP_CTX_KEY,void 0);
const size=computed(()=>ctx?.size??props?.size??"");
const type=computed(()=>ctx?.type??props?.type??"");
const disabled=computed(()=>ctx?.disabled||props.disabled||false);
const handleBtnClick=(e:MouseEvent)=>emits("click",e);//点击事件监听
const handleBtnClickThrottle=throttle(handleBtnClick,props.throttleDuration);//节流版本的点击
//一个响应式的引用存储按钮对于dom元素的引用
defineExpose<ButtonInstance>({
    ref:_ref,
})
</script>
<template>
   <component
    :is="tag"
    ref="_ref"
    class="er-button"
    :outofocus="autofocus"
    :type="tag==='button'?nativeType:0"
    :disabled="disabled||loading?true:void 0"
    :class="{
        [`er-button--${type}`]:type,
        [`er-button--${size}`]:size,
        'is-plain':plain,
        'is-round':round,
        'is-circle':circle,
        'is-disabled':disabled,
        'is-loading':loading,
    }"
    @click="(e:MouseEvent)=>useThrottle?handleBtnClickThrottle(e):handleBtnClick(e)"
   >
   <!-- line40:根据props的节流属性来判断用节流或者是非节流 -->
   <!-- 通过动态绑定来实现对于组件样式的切换，一个是固定的样式，剩余的是动态变化的样式 -->
   <template v-if="loading">
    <slot name="loading">
        <er-icon
        :class="loadingIcon"
        :icon="loadingIcon??'spanner'" 
        :style="iconStyle"
        size="1x"
        spin
        />
    </slot>
   </template>
   <er-icon 
   v-if="icon &&!loading"
   :icon="icon"
   size="1x"
   :style="iconStyle"
   />
   <!-- 如果这两个定义了一个这里需要让位置 -->
   <slot>

   </slot>
</component>

</template>
<style scoped>
@import './style.css'
</style>