<template>
	<div >
		<div :class="classes" :style ="styles">
			<slot></slot>
		</div>
	</div>
</template>

<script>

	//悬停时候的的类选择器被设置为如下值
	//.ivu-affix { position:fixed,z-index:10 }
	const prefixCls = "ivu-affix";

	/**
	 * 获取滚动条相对窗口左上角的位置
	 * @param  target [windows对象]
	 * @param  top    [true取Y轴的值，false或者undefined去X轴的值]
	 * @return {[type]}        [description]
	 */
	function getScroll(target,top){
		//非IE使用该参数
		const prop = top ? "pageYOffset":"pageXOffset";
		//IE使用下列参数
		const method = top ? "scrollTop":"scrollLeft";

		//不同浏览器使用不同的获取方式
		let ret = target[prop];
		if(typeof ret !== 'number'){
			ret = target.document.documentElement[method];
		}

		return ret;
	}
	/**
	 * 获取元素在页面body中的位置
	 * 即元素相对body左上角Y和X的位置，该值不会因拖动浏览器的滚动条而变化
	 * @param  {[type]} element [元素DOM对象]
	 * @return {[type]}         [description]
	 */
	function getOffset(element){
		//元素相对浏览器窗口左上角的位置
		const rect = element.getBoundingClientRect();

		//滚动条相对浏览器窗口位置
		const scrollTop = getScroll(window,true);
		const scrollLeft = getScroll(window);

		//body相对浏览器窗口左上角的位置
		const docEle = window.document.body;
		const clientTop = docEle.clientTop || 0;
		const clientLeft = docEle.clientLeft || 0;

		//返回值
		return {
			top:rect.top + scrollTop - clientTop,
			left:rect.left + scrollLeft - clientLeft
		}
	}

	export default{
		name:'Affix',
		props:{
			offsetTop:{
				type:Number,
				default:0
			},
			offsetBottom:{
				type:Number
			}
		},
		data(){
			return {
				//affix控制是否添加悬停样式  ivu-affix
				affix:false,
				styles:{}
			}
		},
		computed:{
			offsetType(){
				let type = 'top';
				if(this.offsetBottom >=0){
					type="bottom";
				}
				return type
			},
			classes(){
				return [{
					[`${prefixCls}`] : this.affix
				}];
			}
		},
		//组件实例化之后
		mounted(){
            window.addEventListener('scroll', this.handleScroll, false);
            window.addEventListener('resize', this.handleScroll, false);
            console.log(this.affix);
		},
		beforeDestroy(){
			window.removeEventListener('scroll', this.handleScroll, false);
            window.removeEventListener('resize', this.handleScroll, false);
		},
		methods:{
			handleScroll(){
				const affix = this.affix;
				//滚动条相对位置
				const scrollTop = getScroll(window,true);
				const scrollLeft = getScroll(window);
				//获取前组件相对于body的位置
				const elOffset = getOffset(this.$el);
				//窗口文档显示器的高度，不包括菜单，工具栏，滚动条
				const windowHeight = window.innerHeight;
				//组件中第一个div的高度(请查看template，该组件嵌套的div)
				const elHeight = this.$el.getElementsByTagName('div')[0].offsetHeight;
				//停靠在顶部
				//需要悬停
				if( (elOffset.top-this.offsetTop) < scrollTop && this.offsetType == 'top' && !affix ){
					this.affix = true;
					this.styles = {
						top:`${this.offsetTop}px`,
						left:`${elOffset.left}px`,
						width: `${this.$el.offsetWidth}px`
					
					}

					this.$emit('on-change',true);

				}
				 //不需要悬停
				 else if ((elOffset.top - this.offsetTop) > scrollTop && this.offsetType == 'top' && affix){
                    this.affix = false;
                    this.styles = null;
                    this.$emit('on-change', false);

				}


				//停靠底部
                if ((elOffset.top + this.offsetBottom + elHeight) > (scrollTop + windowHeight) && this.offsetType == 'bottom' && !affix) {
                    this.affix = true;
                    this.styles = {
                        bottom: `${this.offsetBottom}px`,
                        left: `${elOffset.left}px`,
                        width: `${this.$el.offsetWidth}px`
                    };

                    this.$emit('on-change', true);
                } else if ((elOffset.top + this.offsetBottom + elHeight) < (scrollTop + windowHeight) && this.offsetType == 'bottom' && affix) {
                    this.affix = false;
                    this.styles = null;

                    this.$emit('on-change', false);
                }

			}
		}

	}
</script>