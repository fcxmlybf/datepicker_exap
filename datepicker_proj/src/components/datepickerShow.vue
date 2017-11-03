<template lang="html">
    <div class="date-div">
        <my-datepicker id="mdate" ref="mdate" :showCalendar="showCalendar" v-model="mySelectDate"></my-datepicker>
        <p>日期：{{mySelectDate}}</p>
        <input v-model="mySelectDate"/>
    </div>
</template>
<script>
    export default {
        data(){
            return{
                mySelectDate:'23',
                showCalendar:false
            }
        },
        created(){
            // 点击其他不在的区域触发事件
//            document.addEventListener('click', (e) => {
//                //if(e.target.attr('type'))
//                console.log(document.getElementById('mdate'));
//                if (!this.$el.contains(e.target)){
//                    this.showCalendar = false;
//                }else{
//                    this.showCalendar = true;
//                }
//            })
        }
    }
</script>
<style lang="scss">

</style>
