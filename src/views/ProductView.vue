<template>
    <div class="container">
        <div class="mt-4">
            <table class="table align-middle">
                <thead>
                    <tr>
                        <th>圖片</th>
                        <th>商品名稱</th>
                        <th>價格</th>
                        <th></th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="product in products" :key="product.key">
                        <td style="width: 200px">
                            <div
                                style="
                                height: 100px;
                                background-size: cover;
                                background-position: center;
                                "
                                :style="{
                                    backgroundImage : `url(${product.imageUrl})`
                                }"
                            ></div>
                        </td>
                        <td>{{ product.title }}</td>
                        <td>
                            <div class="h5" v-if="product.origin_price === product.price">{{ product.price }} 元</div>
                            <div class="div" v-else>
                                <del class="h6">原價 {{ product.original_price }} 元</del>
                                <div class="h5">現在只要 {{ product.price }} 元</div>
                            </div>
                        </td>
                        <td>
                            <div class="btn-group btn-group-sm">
                                <button type="button" class="btn btn-outline-secondary" @click="openModal(product)">
                                    <i class="fas fa-spinner fa-pulse"></i>
                                    查看更多
                                </button>
                                <button type="button" class="btn btn-outline-danger" 
                                :disabled="product.id === status.addCartLoading"
                                @click="addCart(product.id)">
                                <span class="spinner-border spinner-border-sm text-danger" role="status" v-if="product.id === status.addCartLoading">
                                </span>
                                    加到購物車
                                </button>
                            </div>
                        </td>
                    </tr>
                </tbody>
            </table>
            <!-- 購物車列表 -->
            <div class="text-end mt-4">
                <button class="btn btn-outline-danger" type="button" @click="delCartAll()">清空購物車</button>
            </div>
            <table class="table align-middle mt-4">
                <thead>
                    <tr>
                        <th></th>
                        <th>品名</th>
                        <th style="width: 180px">數量/單位</th>
                        <th class="d-flex justify-content-center">單價</th>
                        <th class="text-end">小計</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="cart in carts.carts" :key="cart.id">
                        <td>
                            <button type="button" class="btn btn-outline-danger btn-sm" @click="delCartItem(cart.id)" 
                            :disabled="status.cartQtyLoading === cart.id">
                            <i class="fas fa-spinner fa-pulse"></i>
                            x
                            </button>
                        </td>
                        <td>
                            {{ cart.product.title}}
                            <!-- <div class="text-success">已套用優惠券</div> -->
                        </td>
                        <td>
                            <div class="input-group input-group-sm">
                                <div class="input-group mb-3">
                                    <button type="button" class="border border-primary" @click="cart.qty--; editCart(cart, cart.qty)"
                                    :disabled="cart.qty === 1"
                                    v-if="cart.qty > 1">
                                        -
                                    </button>
                                    <button type="button" class="border border-danger" v-else @click="delCartItem(cart.id)">
                                        <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-trash text-danger" viewBox="0 0 16 16">
                                        <path d="M5.5 5.5A.5.5 0 0 1 6 6v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5m2.5 0a.5.5 0 0 1 .5.5v6a.5.5 0 0 1-1 0V6a.5.5 0 0 1 .5-.5m3 .5a.5.5 0 0 0-1 0v6a.5.5 0 0 0 1 0z"/>
                                        <path d="M14.5 3a1 1 0 0 1-1 1H13v9a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2V4h-.5a1 1 0 0 1-1-1V2a1 1 0 0 1 1-1H6a1 1 0 0 1 1-1h2a1 1 0 0 1 1 1h3.5a1 1 0 0 1 1 1zM4.118 4 4 4.059V13a1 1 0 0 0 1 1h6a1 1 0 0 0 1-1V4.059L11.882 4zM2.5 3h11V2h-11z"/>
                                        </svg>
                                    </button>
                                    <input min="1" type="number" class="form-control text-center" v-model="cart.qty" 
                                    :disabled="cart.id === status.cartQtyLoading" readonly/>
                                    <button type="button" class="border border-primary"  @click="cart.qty++; editCart(cart, cart.qty)">
                                        +
                                    </button>
                                    <span class="input-group-text" id="basic-addon2">
                                        {{ cart.product.unit }}
                                    </span>
                                </div>
                            </div>
                        </td>
                        <td>
                            <div>
                                <span class="input-group d-flex justify-content-center">
                                    {{ cart.product.price }}
                                </span>
                            </div>
                        </td>
                        <td class="text-end">
                            {{ cart.total }}
                        </td>
                    </tr>
                </tbody>
                <tfoot>
                    <tr class="text-end">
                        <td colspan="4">總計:</td>
                        <td class="text-danger">
                            {{ carts.total }}
                        </td>
                    </tr>
                </tfoot>
            </table>
        </div>
    </div>
    <ProductModal :temp-product="tempProduct" :add-cart="addCart" ref="ProductModal"></ProductModal>
</template>

<script>

import axios from 'axios';
import ProductModal from '../components/ProductModal.vue';
const { VITE_API, VITE_PATH } = import.meta.env


export default {
    data() {
        return {
            products: [],
            tempProduct: {},
            carts: {
                
            },
            status: {
                addCartLoading: '',
                cartQtyLoading: ''
            }
        }
    },
    methods: {
        getProducts() {
            const api =`${VITE_API}/api/${VITE_PATH}/products/all`;
                axios.get(api)
                    .then(res => {
                        this.products = res.data.products;
                        // console.log(res.data);
                    })
                    .catch(err => {
                        console.log(err.response.data);
                    })
        },
        openModal(product) {
            this.tempProduct = product;
            console.log(product);
            this.$refs.ProductModal.openModal();
        },
        addCart(product_id, qty = 1) {
            const order = {
                product_id,
                qty,
            };
            this.status.addCartLoading = product_id;
            const api =`${VITE_API}/api/${VITE_PATH}/cart`;
                axios.post(api, {data: order})
                    .then(res => {
                        console.log(res.data);
                        this.status.addCartLoading = '';
                        this.getCart();
                        this.$refs.ProductModal.hideModal();
                    })
                    .catch(err => {
                        console.log(err.response.data);
                    });
        },
        getCart() {
            const api =`${VITE_API}/api/${VITE_PATH}/cart`;
                axios.get(api)
                    .then(res => {
                        this.carts = res.data.data;
                        // console.log(this.carts.carts);
                    })
                    .catch(err => {
                        console.log(err.response.data);
                    });
        },
        editCart(item, qty = 1) {
            const order = {
                product_id: item.product.id,
                qty,
            };
            this.status.cartQtyLoading = item.id;
            const api =`${VITE_API}/api/${VITE_PATH}/cart/${item.id}`;
                axios.put(api, {data: order})
                    .then(res => {
                        // console.log(res);
                        this.status.cartQtyLoading = '';
                        this.getCart();
                    })
                    .catch(err => {
                        console.log(err.response.data);
                    });
        },
        delCartItem(id) {
            this.status.cartQtyLoading = id;
            const api =`${VITE_API}/api/${VITE_PATH}/cart/${id}`;
                axios.delete(api)
                    .then(res => {
                        console.log(res);
                        this.status.cartQtyLoading = '';
                        this.getCart();
                    })
                    .catch(err => {
                        console.log(err.response.data);
                    });
        },
        delCartAll() {
            const api =`${VITE_API}/api/${VITE_PATH}/carts`;
                axios.delete(api)
                    .then(res => {
                        // console.log(res);
                        this.getCart();
                    })
                    .catch(err => {
                        console.log(err.response.data);
                    });
        }
    },
    components: {
        ProductModal,
    },
    mounted() {
        this.getProducts();
        this.getCart();
    }
}
</script>