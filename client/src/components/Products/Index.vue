<template>
    <div class="bg">
        <div class="blog-header">
 <center><h2>ส่วนจัดการGUNPLA</h2></center>
 <div>
 <form>
 <input type="text" v-model="search" placeholder="Search">
 </form>
</div>
 <div><br>
 <button class="btn btn-success" v-on:click="navigateTo('/product/create')">สร้าง GUNPLA</button>
 <strong> จํานวนGUNPLA: </strong> {{products.length}}<strong> กล่อง </strong></div>
 <br>
 </div>

<center><img src="@/assets/79.png"  /></center><br>
 <div v-if="products.length === 0" class="empty-blog">
 *** ไม่มีข้อมูล***
 </div>

 <div v-for="product in products" v-bind:key="product.id" class="blog-list">
 <!-- <p>id: {{ blog.id }}</p> -->
 <div class="blog-pic">
 <transition name="fade">
 <div class="thumbnail-pic" v-if="product.thumbnail != 'null'">
 <img :src="BASE_URL+product.thumbnail" alt="thumbnail">
 </div>
 </transition>
 </div>

 <h3>{{ product.name }}</h3>
 <p><strong>ประเภท:</strong> {{ product.taste }}</p>
 <p><strong>ราคา: </strong>{{ product.price }}<strong> บาท</strong></p>
 <p><strong>สถานะ:</strong> {{ product.status }}</p>

 <!-- <p>status: {{ blog.status }}</p> -->
 <p>
 <button class="btn btn-primary" v-on:click="navigateTo('/product/'+ product.id)">ดูข้อมูลGUNPLA</button>
 <button class="btn btn-warning" v-on:click="navigateTo('/product/edit/'+ product.id)">แก้ไข blog</button>
 <button class="btn btn-danger" v-on:click="deleteBlog(product)">ลบข้อมูล</button>
 </p>
 </div>

 <div class="clearfix"></div>
 </div>

</template>
<script>
    import ProductService from '@/services/ProductService'
    import _ from 'lodash'
    export default {
        data () {
            return {
                search: '',
                products: [],
                BASE_URL: "http://localhost:8081/assets/uploads/",
                pictures: [],
            }
            
        },
        watch: {
                search: _.debounce(async function (value) {
                const route = {
                    name: 'products'
                }
                if(this.search !== '') {
                    route.query = {
                        search: this.search
                    }
                }

                console.log('search: ' + this.search)
                this.$router.push(route)
                }, 700),

                '$route.query.search': {
                    immediate: true,
                    async handler (value) {
                        this.products = (await ProductService.index(value)).data
                    }
                }
            },
        async created () {
            this.products = (await ProductService.index()).data
        },
        methods: {
            logout () {
                this.$store.dispatch('setToken', null)
                this.$store.dispatch('setBlog', null)
                this.$router.push({
                    name: 'login'
                })
            },
            navigateTo (route) {
                this.$router.push(route)
            },
            async deleteBlog (product) {
                let result = confirm("Want to delete?")
                if (result) {
                    try {
                        await ProductService.delete(product)
                        this.refreshData()
                    } catch (err) {
                        console.log(err)
                    }
                }
            },
            async refreshData() {
                this.products = (await ProductService.index()).data
            }
        }
    }
</script>
<style scoped>
.empty-blog {
 width: 100%;
 text-align: center;
 padding:10px;
 background:darksalmon;
 color:white;
}
.button {
    background-color: #4CAF50; /* Green */
    border: none;
    color: white;
    padding: 15px 32px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
}
/* thumbnail */
.thumbnail-pic img{
 width: 200px;
 padding: 5px 10px 0px 0px;
}
.blog-info {
 float: left;
}
.blog-pic {
 float: left;
}
.clearfix {
 clear: both;
}
.blog-list {
    background-color: snow;
 border:solid 1px #dfdfdf;
 margin-bottom: 10px;
 max-width: 900px;
 margin-left: auto;
 margin-right: auto;
 padding: 5px;
 box-shadow: 0 2px 4px 0 rgba(0,0,0,.1);
}
.blog-header {
 max-width: 900px;
 margin-left: auto;
 margin-right: auto;
}
.bg{
    background-color: rgb(214, 173, 238);
}
</style>