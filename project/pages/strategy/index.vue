<template>
    <div class="container">
        <Header/>
        <div id="checkboxes">
            <p>籌碼指標</p>
            <input type="checkbox" value="foreignBuy" v-model="strategy.selected" @change="showIntersectionCount()">
            <label>外資連三日買超共 {{ result.foreignBuyCount }} 檔</label>
            <input type="checkbox" value="domesticBuy" v-model="strategy.selected" @change="showIntersectionCount()">
            <label>投信連三日買超共 {{ result.domesticBuyCount }} 檔</label>
            <br>
            <p>型態指標</p>
            <input type="checkbox" value="maRise" v-model="strategy.selected" @change="showIntersectionCount()">
            <label>均線多頭排列共 {{ result.maRiseCount }} 檔</label>
            <input type="checkbox" value="maInterlace" v-model="strategy.selected" @change="showIntersectionCount()">
            <label>突破均線糾結共 {{ result.maInterlaceCount }} 檔</label>
            <input type="checkbox" value="hitHigh20d" v-model="strategy.selected" @change="showIntersectionCount()">
            <label>收盤價創20日新高共 {{ result.hitHigh20dCount }} 檔</label>
        </div>
        <div class="row justify-content-center">
            <button type= "button" class="btn btn-danger" @click="showIntersection()">共{{strategy.intersectionCount}}檔 顯示</button>
        </div>
        <br>
        <table class="table">
            <thead>
                <tr>
                    <th style="width: 6%">股號</th>
                    <th style="width: 8%">簡稱</th>
                    <th style="width: 8%">日期</th>
                    <th style="width: 8%">產業</th>
                    <th style="width: 8%">收盤價</th>
                    <th style="width: 8%">殖利率(%)</th>
                </tr>
            </thead>
            <tbody>
                <tr  v-for= "(stock, index) in strategy.intersectionObj" :key="index">
                    <td>{{ index }}</td>
                    <td>{{ stock.name }}</td>
                    <td>{{ stock.date }}</td>
                    <td>{{ stock.industry }}</td>
                    <td>{{ stock.close }}</td>
                    <td>{{ stock.yield }}</td>
                </tr>
            </tbody>
        </table>
        <Footer/>
    </div>
</template>

<script>
import Header from '~/components/Header.vue'
import Footer from '~/components/Footer.vue'
import axios from 'axios' 
export default {
    components: {
        Header,
        Footer
    },
    data () {
        return {
            strategy:{
                selected:[],
                intersectionId:[],
                intersectionCount:0,
                intersectionObj:{},
            }
        }
    },
    async asyncData() { 
        const path = 'http://localhost:5000/strategyResult'
        let {data}  = await axios.get(path)
        
        return { 
            result: data,            
        }
    },
    methods: {
        showIntersectionCount() {
            let selected = this.strategy.selected 
            let result = this.result
            let target = []
            
            if (selected.length > 0 ){
                for (let i in selected){
                    target.push(Object.keys(result[selected[i]]))
                }
                this.strategy.intersectionId = target.reduce((a, b) => a.filter(c => b.includes(c)));
            }else{
                this.strategy.intersectionId = []
            }

            this.strategy.intersectionCount = this.strategy.intersectionId.length
        },
        showIntersection() {
            let intersectionId = this.strategy.intersectionId
            let result = this.result
            let resultObj = {}
            
            for (let i in intersectionId){
                resultObj[intersectionId[i]] = result.conditionUnion[intersectionId[i]] 
            }
            this.strategy.intersectionObj = resultObj
        }
    }
}
</script>

<style>
#checkboxes p {
    display:inline-block;
    margin:10px;
}
#checkboxes input {
    display:inline-block;
    margin:10px;
}
#checkboxes label {
    display:inline-block;
    margin:10px;
}

</style>
