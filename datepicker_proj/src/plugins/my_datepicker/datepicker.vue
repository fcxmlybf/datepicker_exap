<template>
	<div class="date_picker">
		<!--<div class="input" v-text="getDateValue" @click="panelState=!panelState" :value="getDateValue"></div>-->
        <input type="text" ref="input" :value="outValue" class="input" readonly="true" @blur="inpBlur" @focus="inpFocus">
		<div class="date-panel" v-show="panelState">
			<div class="panel-header" v-show="panelType !== 'year'">
				<div class="arrow-left" @click="prevMonthPreview()">&lt;</div>
				<div class="year-month-box">
					<div class="year-box">{{tmpYear}}</div>
					<div class="month-box">{{tmpMonth + 1}}</div>
				</div>
				<div class="arrow-right" @click="nextMonthPreview()">&gt;</div>
				<div class="clear"></div>
			</div>
			<div class="type-date" v-show="panelType === 'date'">
				<ul class="weeks">
					<li v-for="item in weekList">{{item}}</li>
				</ul>
				<ul class="date-list">
					<li v-for="(item,index) in dateList"
                        :class="{preMonth: item.prevMonth, nextMonth: item.nextMonth, today:item.today, firstItem:(index % 7) === 0,selected:item.value==selectDate&&item.curMonth}"
                        @click="setDateValue(item)">
						<div class="message">
							<div class="bg"></div><span v-text="item.value"></span>
						</div>
					</li>
				</ul>
			</div>
		</div>
	</div>
</template>
<script>
	export default {
		props:{
			value:String,
            showCalendar:Boolean
		},
		data(){
			let now = new Date()
			return{
				panelState: false,
				panelType: 'date',
				year: now.getFullYear(),
                month: now.getMonth(),
                today:now.getDate(),
				tmpYear: now.getFullYear(),
				tmpMonth: now.getMonth(),
                tmpDay:now.getDate(),
                selectDate:0,
                outValue:this.value,
                turnMonth:false,
                timer:null,
                
				
				weekList: ['日', '一', '二', '三', '四', '五', '六'],
			}
		},
		computed:{
			dateList(){
				//获取当月天数
				let curMonthLength = new Date(this.tmpYear, this.tmpMonth+1, 0).getDate();
				//先将当月的日期塞入dateList
				let dateList = Array.from({length: curMonthLength}, (val, index) => {
				    if((index+1)==this.today&&this.tmpMonth==this.month&&this.tmpYear==this.year){
                        return {
                            curMonth: true,
                            today:true,
                            value: index + 1
                        }
                    }else{
                        return {
                            curMonth: true,
                            value: index + 1
                        }
                    }
				});
				//获取当月1号的星期是为了确定在1号前需要插多少天
				let startDay = new Date(this.tmpYear,this.tmpMonth,1).getDay();        //??
				let prevMonthLength = new Date(this.tmpYear, this.tmpMonth, 0).getDate()
				console.log(prevMonthLength);
				//在1号前插入上个月日期
				for(let i = 0; i < startDay; i++){
					dateList = [{prevMonth: true, value: prevMonthLength - i}].concat(dateList)
				}
				//补全剩余位置
				for(let i = 1; i <= (42-curMonthLength-startDay); i++){
					dateList[dateList.length] = {nextMonth: true, value: i}
				}
				
				return dateList;
			},
//            getDateValue:function(){
//			    this.outValue = `${this.tmpYear}-${this.tmpMonth+1}-${this.tmpDay}`;
//                this.$emit('input',this.outValue);
//			    return `${this.tmpYear}-${this.tmpMonth+1}-${this.tmpDay}`;
//            }
		},
        watch:{
//            getDateValue(cur){
//                this.$emit('input',this.getDateValue);
//            }
            outValue(cur){
                this.$emit('input',cur);
            },
            value(cur){
                this.outValue = cur;
            },
//            panelState(cur){
//                console.log(cur);
//            }
//            showCalendar(cur){
//                console.log(cur);
//            }
        },
		methods:{
			prevMonthPreview(){
				//this.tmpMonth = this.tmpMonth === 0 ? 0 : this.tmpMonth - 1         //???
                if(this.tmpMonth<1){
                    this.tmpMonth=11;
                    this.tmpYear--;
                }else{
                    this.tmpMonth--;
                }
                this.changeDate();
                this.$refs.input.focus()
                //this.turnMonth = true;
			},
			nextMonthPreview(){
				//this.tmpMonth = this.tmpMonth === 11 ? 11 : this.tmpMonth + 1       //???
                if(this.tmpMonth>10){
                    this.tmpMonth=0;
                    this.tmpYear++;
                }else{
                    this.tmpMonth++;
                }
                this.changeDate();
                this.$refs.input.focus()
                //this.turnMonth = true;
			},
            setDateValue(item){
			    this.selectDate = item.value;
			    if(item.prevMonth){
			        this.prevMonthPreview();
                }else if(item.nextMonth){
			        this.nextMonthPreview();
                };
			    this.tmpDay = item.value;
			    
			    this.changeDate();
			    //this.panelState = false;
            },
            changeDate(){
                this.outValue = `${this.tmpYear}-${this.tmpMonth+1}-${this.tmpDay}`;
                this.turnMonth = false;
            },
            inpBlur(){
                clearTimeout(this.timer);
                this.timer=setTimeout(()=>{
                    this.panelState = false;
                },100);
                //this.turnMonth = false;
            },
            inpFocus(){
                clearTimeout(this.timer);
                this.timer=setTimeout(()=>{
                    this.panelState = true;
                },100);
            }
		}
	}
</script>
<style lang="scss">
	ul{
		padding: 0;
		margin: 0;
		list-style: none;
	}
	.clear{clear: both;}
	.date_picker{
		position: relative;
        text-align: left;
        display: inline-block;
		.input{
            height: 30px;
            width: 100px;
            border:1px solid #ccc;
			font-size: inherit;
			padding-left: 4px;
			box-sizing: border-box;
			outline: none;
		}
		.date-panel{
			font-family: microsoft yahei;
			position: absolute;
			z-index: 5000;
			border: 1px solid #eee;
			width: 250px;
			padding: 5px 10px 10px;
			box-sizing: border-box;
			background-color: #fff;
			transform: translateY(4px);
			.arrow-left, .arrow-right {
				float:left;width:20%;
				-webkit-box-flex: 1;
				-ms-flex: 1;
				flex: 1;
				font-size: 17px;
				background-color: #fff;
				text-align: center;
				cursor: pointer;
			}
			.year-month-box{
				float:left;width:60%;text-align: center;
				.year-box, .month-box {
					display: inline-block;margin:0px 10px;
					transition: all ease .1s;
					font-family: Roboto, sans-serif;
					-webkit-box-flex: 1;
					-ms-flex: 1;
					flex: 1;
					text-align: center;
					font-size: 15px;
					line-height: 2;
					cursor: pointer;
					background-color: #fff;
				}
			}
			
			.type-date{
				.weeks{text-align: center;
					li{
						display: inline-block;
						font-family: Roboto;
						width: 30px;
						height: 30px;
						text-align: center;
						line-height: 30px;
					}
				}
				.date-list{text-align: center;
					li{
						display: inline-block;
						font-family: Roboto;
						width: 30px;
						height: 30px;
						text-align: center;
						line-height: 30px;
                        cursor: default;
                        &.today{background-color: burlywood;}
                        &.selected{background-color: cornflowerblue;color:#fff;}
					}
					.preMonth,.nextMonth {
						color: #ccc;
					}
				}
			}
			
			
		}
	}
</style>
