<template>
    <div>
        <h1>รายละเอียดGUNPLA</h1>
        
        <p>ชื่อGUNPLA: {{ product.name }}</p>
        <p>หมวดหมู่: {{ product.taste }}</p>
        <p>ราคา: {{ product.price }}</p>
        <p>สถานะ: {{ product.status }}</p>
        <p>
        <button class="btn btn-outline-primary" v-on:click="navigateTo('/products')">กลับ </button>
        </p>
    </div>
</template>
<script>
    import ProductService from '@/services/ProductService'
    export default {
        data () {
            return {
                product: null
            }
        },
        async created () {
            try {
                let productId = this.$route.params.productId
                this.product = (await ProductService.show(productId)).data
            } catch (error) {
                console.log (error)
            }
        },
        methods : {
            navigateTo (route) {
                this.$router.push(route)
            },
        }
    }
</script>
<style scoped>

</style>