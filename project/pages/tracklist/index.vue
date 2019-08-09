<template>
    <div class="container">
        <Header/>
        <div>
            <nav class="navbar navbar-expand-sm bg-light justify-content-center">
                <ul class="navbar-nav">
                    <li class="nav-item">
                        <a class="nav-link" @click= "info='price'"> 盤後資訊</a>
                    </li>
                    <li class="nav-item">
                        <a class="nav-link" @click= "info='value'"> 價值評估 </a>
                    </li>
                     <li class="nav-item">
                         <a class="nav-link" @click= "info='revenue'"> 營收概況 </a>
                    </li>
                </ul>
            </nav>    
        </div>
        <br>
        <div class="row justify-content-center">
            <input type="text" name="stockid" v-model="stockid" maxlength="4">
            <button type="submit" class="btn btn-success btn-sm" @click="addStock()">Add</button>
        </div> 
        <br>
        <table class="table">
            <thead> 
                <tr>
                    <th style="width: 6%">股號</th>
                    <th style="width: 8%">簡稱</th>
                    <th style="width: 10%"> 產業</th>
                    <th style="width: 15%" v-if= "info ==='price'">收盤價(元)</th>
                    <th style="width: 15%" v-if= "info ==='price'">成交量(張)</th>
                    <th style="width: 15%" v-if= "info ==='value'">本益比</th>
                    <th style="width: 15%" v-if= "info ==='value'">殖利率(％)</th>
                    <th style="width: 15%" v-if= "info ==='revenue'">月營收(仟萬元)</th>
                    <th style="width: 15%" v-if= "info ==='revenue'">年成長率(％)</th>
                    <th style="width: 15%" ></th>
                </tr>
            </thead>
            <tbody>
                <tr  v-for= "(stock, index) in result.stocks" :key="index">
                    <td>{{ index }}</td>
                    <td>{{ stock.name }}</td>
                    <td>{{ stock.industry }}</td>
                    <td v-if= "info ==='price'">{{ stock.close }}</td>
                    <td v-if= "info ==='price'">{{ stock.volume }}</td>
                    <td v-if= "info ==='value'">{{ stock.per }}</td>
                    <td v-if= "info ==='value'">{{ stock.yield }}</td>
                    <td v-if= "info ==='revenue'">{{ stock.mon }}</td>
                    <td v-if= "info ==='revenue'">{{ stock.yoy }}</td>
                    <td>
                        <button type= "button" class="btn btn-danger btn-sm" @click="removeStock(index)">Delete</button>
                    </td>
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
    data() {
        return {
            info:'price',
            stockid: '',
        }
    },
    async asyncData() { 
        const path = 'http://localhost:5000/tracklist'
        let {data}  = await axios.get(path)
        return { 
            result: data,            
        }
    },
    methods: {
        async getStocks() {
            const path = 'http://localhost:5000/tracklist'
            try {
                let {data} = await axios.get(path)
                this.result = data
            }catch(err){
                 console.log(err)
            }
        },
        async addStock () {
            const path = 'http://localhost:5000/tracklist'
            const formData = {
                stockid: this.stockid
            }
            try {
                await axios.post(path, formData)
                await this.getStocks()
            }catch(err){
                console.log(err)
            } 
        },
        async removeStock (stockid) {
            const path = `http://localhost:5000/tracklist/${stockid}`
            try {
                await axios.delete(path)
                await this.getStocks()
            }
            catch(err){
                console.log(err)
            }
        },
    }


}
</script>