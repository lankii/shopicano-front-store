<template>
    <div>
        <Header/>
        <Navigation logo="../images/logo.png"/>

        <!-- main wrapper -->
        <div class="main-wrapper">

            <Home/>
            <Services/>
            <NewsLetter/>
            <Footer/>
        </div>
        <!-- /main wrapper -->
    </div>
</template>

<script>
    import Header from "@/components/indexComponents/Header";
    import Navigation from "@/components/indexComponents/Navigation";
    import Footer from "@/components/indexComponents/Footer";
    import NewsLetter from "@/components/indexComponents/NewsLetter";
    import Services from "@/components/indexComponents/Services";
    import Home from "@/components/indexComponents/Home";

    export default {
        name: "Index",
        components: {Home, Services, NewsLetter, Footer, Navigation, Header}
    }
</script>
