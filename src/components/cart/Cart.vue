<template>
    <div>
        <Header/>
        <Navigation/>

        <!-- main wrapper -->
        <div class="main-wrapper">
            <!-- breadcrumb -->
            <nav class="bg-gray py-3">
                <div class="container">
                    <ol class="breadcrumb">
                        <li class="breadcrumb-item">
                            <router-link to="/">Home</router-link>
                        </li>
                        <li class="breadcrumb-item active" aria-current="page">Cart</li>
                    </ol>
                </div>
            </nav>
            <!-- /breadcrumb -->

            <div class="section">
                <div class="cart shopping">
                    <div class="container">
                        <div class="row">
                            <div class="col-md-10 mx-auto">
                                <div class="block">
                                    <div class="product-list">
                                        <form>
                                            <div class="table-responsive">
                                                <table class="table cart-table">
                                                    <thead>
                                                    <tr>
                                                        <th/>
                                                        <th>Product Name</th>
                                                        <th>Price</th>
                                                        <th>Quantity</th>
                                                        <th>Sub Total</th>
                                                    </tr>
                                                    </thead>

                                                    <div v-if="isCartNil">
                                                        <h4 class="my-3 text-center text-danger">You have no selected
                                                            item in cart.</h4>
                                                    </div>

                                                    <tbody>
                                                    <tr v-for="item in getFullCart" v-bind:key="item.itemID">
                                                        <td>
                                                            <a @click="onClickRemoveItem(item.itemID)"
                                                               class="product-remove">&times;</a>
                                                        </td>
                                                        <td>
                                                            <div class="product-info">
                                                                <img class="img-fluid w-25" :src="item.itemThumbnail"
                                                                     alt="product-img"/>
                                                                <a class="ml-4">{{ item.itemName }}</a>
                                                            </div>
                                                        </td>
                                                        <td>${{ formatPrice(item.itemPrice) }}</td>
                                                        <td>
                                                            <input type="number"
                                                                   v-bind:value="item.itemQuantity"
                                                                   @keydown="$event.key === '-' ? $event.preventDefault() : null"
                                                                   @keyup="changeQuantity(item,item.itemID, $event.target.value)"
                                                                   min="0">
                                                        </td>
                                                        <td>${{ formatPrice(item.subTotal) }}</td>
                                                    </tr>
                                                    </tbody>
                                                </table>
                                            </div>
                                            <hr>
                                            <div class="row">
                                                <div class="col-12">
                                                    <ul class="list-unstyled text-right">
                                                        <li>Grand Total <span class="d-inline-block w-100px">${{ formatPrice(grandTotalPrice) }}</span>
                                                        </li>
                                                    </ul>
                                                </div>
                                            </div>
                                            <hr>
                                            <button
                                                    :disabled="isCartNil"
                                                    @click="onClickCheckout"
                                                    class="btn btn-primary float-right">Checkout
                                            </button>
                                        </form>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <Footer/>
        </div>
        <!-- /main wrapper -->
    </div>
</template>

<script>
    /* eslint-disable */
    import Header from "@/components/indexComponents/Header";
    import Navigation from "@/components/indexComponents/Navigation";
    import Footer from "@/components/indexComponents/Footer";
    import SessionStore from "@/common/session_store";
    import NumberUtil from "../../common/number";

    export default {
        name: "Cart",
        components: {Footer, Navigation, Header},
        data() {
            return {
                isCartNil: true,
                quantity: []
            };
        },
        computed: {
            getFullCart() {
                this.isCartNil = this.$store.getters.cartItemCount < 1;
                if (this.isCartNil) {
                    this.$store.dispatch('storeIsProductDigitalAction', '');
                    return this.$router.push('/shop');
                }
                return this.$store.getters.getCart;
            },
            grandTotalPrice() {
                return this.$store.getters.cartTotalPrice;
            }
        },
        methods: {
            onClickRemoveItem: function (id) {
                this.removeFromCart(id)
            },
            removeFromCart: function (itemId) {
                this.$store.dispatch('removeItemFromAction', itemId)
            },
            changeQuantity: function (item, itemId, qntt) {
                console.log(itemId, qntt);

                if (qntt === "") {
                    qntt = 0;
                }
                this.$store.dispatch('changeQuantityAction', {itemId, qntt});
                console.log(itemId, qntt);
            },
            onClickCheckout: function () {
                /*if (!this.$store.getters.getterIsAllProductDigital) {
                    this.$router.push('/payment')
                }*/
                if (SessionStore.IsLoggedIn()) {
                    this.$router.push('/billing')
                } else {
                    localStorage.setItem('redirect_to', this.$route.path);
                    this.$router.push('/login');
                }
            },
            formatPrice: function (v) {
                return NumberUtil.toDisplayUnit(v);
            },
            isOutOfStock: function (product) {
                console.log(product);

                if (product.is_digital) {
                    return false;
                }
                return product.stock <= 0
            },
            isInLimit: function (q, p) {
                return q < p.max_quantity_count && q < p.stock;
            }
        }
    }
</script>

<style scoped>
    input[type=number]::-webkit-inner-spin-button,
    input[type=number]::-webkit-outer-spin-button {
        -webkit-appearance: none;
        margin: 0;
    }
</style>
