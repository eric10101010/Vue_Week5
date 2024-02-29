<template>
    <div class="container">
        <loading v-model:active="isLoading">
            <div class="loadingio-spinner-spin-pqarappzpn"><div class="ldio-eviqoo58lam">
            <div><div></div></div><div><div></div></div><div><div></div></div><div><div></div></div><div><div></div></div><div><div></div></div><div><div></div></div><div><div></div></div>
            </div></div>
        </loading>
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
                    <tr v-for="product in products" :key="product.id">
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
                <button class="btn btn-outline-danger" type="button" @click="delCartAll()"
                :disabled="carts.carts.length === 0">
                清空購物車</button>
            </div>
            <table class="table align-middle mt-4" v-if="carts.carts.length !== 0">
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
                                    <input type="number" id="cartNumber" class="form-control text-center" v-model="cart.qty" 
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
            <div class="mt-3" v-else>
                <h3 class="text-danger text-center">目前購物車是空的</h3>
            </div>
        </div>
        <div class="my-5 row justify-content-center">
            <VeeForm ref="form" class="col-md-6" v-slot="{ errors }" @submit="createOrder">
                <div class="mb-3">
                    <label for="email" class="form-label">Email</label>
                    <VeeField id="email" name="email" type="email" class="form-control" :class="{ 'is-invalid': errors['email'] }"
                            placeholder="請輸入Email" rules="email|required" v-model="form.user.email" autocomplete="off">
                    </VeeField>
                    <Error-message name="email" class="invalid-feedback"></Error-message>
                </div>
                <div class="mb-3">
                    <label for="name" class="form-label">收件人姓名</label>
                    <VeeField id="name" name="姓名" type="text" class="form-control" :class="{ 'is-invalid': errors['姓名'] }"
                            placeholder="請輸入姓名" rules="required" v-model="form.user.name" autocomplete="off">
                    </VeeField>
                    <Error-message name="姓名" class="invalid-feedback"></Error-message>
                </div>
                <div class="mb-3">
                    <label for="tel" class="form-label">收件人電話</label>
                    <VeeField id="tel" name="電話" type="tel" class="form-control" :class="{ 'is-invalid': errors['電話'] }"
                            placeholder="請輸入電話" :rules="isPhone"  v-model="form.user.tel" autocomplete="off">
                    </VeeField>
                    <Error-message name="電話" class="invalid-feedback"></Error-message>
                </div>
                <div class="mb-3">
                    <label for="address" class="form-label">收件人地址</label>
                    <VeeField id="address" name="地址" type="text" class="form-control" :class="{ 'is-invalid': errors['地址'] }"
                            placeholder="請輸入地址" rules="required" v-model="form.user.address" autocomplete="off">
                    </VeeField>
                    <Error-message name="地址" class="invalid-feedback"></Error-message>
                </div>
                <div class="mb-3">
                    <label for="message" class="form-label">留言</label>
                    <textarea id="message" class="form-control" cols="30" rows="10" v-model="form.message" autocomplete="off"></textarea>
                </div>
                <div class="text-end">
                    <button type="submit" class="btn btn-danger">
                        送出訂單
                    </button>
                </div>
            </VeeForm>
        </div>
    </div>
    <ProductModal :temp-product="tempProduct" :add-cart="addCart" ref="ProductModal"></ProductModal>
</template>

<script>

import axios from 'axios';
import ProductModal from '../components/ProductModal.vue';
import Loading from 'vue-loading-overlay';
import Swal from 'sweetalert2';

const { VITE_API, VITE_PATH } = import.meta.env


export default {
    data() {
        return {
            products: [],
            tempProduct: {},
            carts: {},
            isLoading: false,
            form: {
                user: {
                    name: '',
                    email: '',
                    tel: '',
                    address: '',
                },
                message: '',
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
            const vm = this;
            vm.isLoading = true;
                axios.get(api)
                    .then(res => {
                        this.products = res.data.products;
                        vm.isLoading = false;
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
                        const name = res.data.data.product.title;
                        this.status.addCartLoading = '';
                        Swal.fire({
                                title: `成功加入 ${name} 到購物車`,
                                icon: 'success',
                                confirmButtonText: '確認'
                        })
                        this.getCart();
                        this.$refs.ProductModal.hideModal();
                    })
                    .catch(err => {
                        console.log(err.res.data);
                        Swal.fire({
                                title: `加入購物車失敗`,
                                icon: 'error',
                                confirmButtonText: '確認'
                        })
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
                        let message = res.data.message;
                        this.status.cartQtyLoading = '';
                        Swal.fire({
                                title: `商品${message}`,
                                icon: 'success',
                                confirmButtonText: '確認'
                        })
                        this.getCart();
                    })
                    .catch(err => {
                        console.log(err.response.data);
                        Swal.fire({
                                title: `刪除失敗`,
                                icon: 'error',
                                confirmButtonText: '確認'
                        })
                    });
        },
        delCartAll() {
            const api =`${VITE_API}/api/${VITE_PATH}/carts`;
                axios.delete(api)
                    .then(res => {
                        // console.log(res);
                        Swal.fire({
                                title: `成功清空購物車`,
                                icon: 'success',
                                confirmButtonText: '確認'
                        })
                        this.getCart();
                    })
                    .catch(err => {
                        console.log(err.response.data);
                    });
        },
        createOrder() {
            // this.isLoading = true;
            const api =`${VITE_API}/api/${VITE_PATH}/order`;
            const order = this.form;
                axios.post(api, { data: order }).then((res) => {
                    // this.$router.push(`/user/checkout/${response.data.orderId}`);
                    this.$refs.form.resetForm();
                    this.getCart();
                    // this.isLoading = false;
            }).catch((err) => {
                console.dir(err);
            });
        },
        isPhone(value) {
            const phoneNumber = /^(09)[0-9]{8}$/
            return phoneNumber.test(value) ? true : '需要正確的電話號碼'
        }
    },
    components: {
        ProductModal,
        Loading,
        Swal
    },
    computed: {
        checkData() {
            const attrs = ['name', 'email', 'tel', 'address'];
            return attrs.every((item) => this.form.user[item] !== '');
        },
    },
    mounted() {
        this.getProducts();
        this.getCart();
    }
}
</script>

<style type="text/css">
    @keyframes ldio-eviqoo58lam {
        0% {
            opacity: 1;
            backface-visibility: hidden;
            transform: translateZ(0) scale(1.5,1.5);
        } 100% {
            opacity: 0;
            backface-visibility: hidden;
            transform: translateZ(0) scale(1,1);
        }
    }
    .ldio-eviqoo58lam div > div {
        position: absolute;
        width: 24px;
        height: 24px;
        border-radius: 50%;
        background: #3e6d8d;
        animation: ldio-eviqoo58lam 1s linear infinite;
    }.ldio-eviqoo58lam div:nth-child(1) > div {
        left: 148px;
        top: 88px;
        animation-delay: -0.875s;
    }
    .ldio-eviqoo58lam > div:nth-child(1) {
        transform: rotate(0deg);
        transform-origin: 160px 100px;
    }.ldio-eviqoo58lam div:nth-child(2) > div {
        left: 130px;
        top: 130px;
        animation-delay: -0.75s;
    }
    .ldio-eviqoo58lam > div:nth-child(2) {
        transform: rotate(45deg);
        transform-origin: 142px 142px;
    }.ldio-eviqoo58lam div:nth-child(3) > div {
        left: 88px;
        top: 148px;
        animation-delay: -0.625s;
    }
    .ldio-eviqoo58lam > div:nth-child(3) {
        transform: rotate(90deg);
        transform-origin: 100px 160px;
    }.ldio-eviqoo58lam div:nth-child(4) > div {
        left: 46px;
        top: 130px;
        animation-delay: -0.5s;
    }
    .ldio-eviqoo58lam > div:nth-child(4) {
        transform: rotate(135deg);
        transform-origin: 58px 142px;
    }.ldio-eviqoo58lam div:nth-child(5) > div {
        left: 28px;
        top: 88px;
        animation-delay: -0.375s;
    }
    .ldio-eviqoo58lam > div:nth-child(5) {
        transform: rotate(180deg);
        transform-origin: 40px 100px;
    }.ldio-eviqoo58lam div:nth-child(6) > div {
        left: 46px;
        top: 46px;
        animation-delay: -0.25s;
    }
    .ldio-eviqoo58lam > div:nth-child(6) {
        transform: rotate(225deg);
        transform-origin: 58px 58px;
    }.ldio-eviqoo58lam div:nth-child(7) > div {
        left: 88px;
        top: 28px;
        animation-delay: -0.125s;
    }
    .ldio-eviqoo58lam > div:nth-child(7) {
        transform: rotate(270deg);
        transform-origin: 100px 40px;
    }.ldio-eviqoo58lam div:nth-child(8) > div {
        left: 130px;
        top: 46px;
        animation-delay: 0s;
    }
    .ldio-eviqoo58lam > div:nth-child(8) {
        transform: rotate(315deg);
        transform-origin: 142px 58px;
    }
    .loadingio-spinner-spin-pqarappzpn {
        width: 200px;
        height: 200px;
        display: inline-block;
        overflow: hidden;
        background: transparent;
    }
    .ldio-eviqoo58lam {
        width: 100%;
        height: 100%;
        position: relative;
        transform: translateZ(0) scale(1);
        backface-visibility: hidden;
      transform-origin: 0 0; /* see note above */
    }
    .ldio-eviqoo58lam div { box-sizing: content-box; }
</style>