<template>
  <div class="vendor-slug" v-if="getVendorInfo">
    <social-head v-if="getVendorInfo && getVendorInfo.vendor_name" :image="getVendorInfo.display_picture && getVendorInfo.display_picture" :title="getVendorInfo.vendor_name && getVendorInfo.vendor_name" :description="getVendorInfo.tag_line && getVendorInfo.tag_line" />

    <div class="vendor-slug__sections">
      <page-section class="--full-width --no-padding-x --no-padding-y">
        <vendor-banner v-if="getVendorInfo" :vendorInfo="getVendorInfo" :ratings="getRatings" />
      </page-section>

      <!-- About Us Section -->
      <page-section v-if="getVendorInfo.description">
        <div class="heading --underline --center mb">About Us</div>
        <about-store :about="getVendorInfo.description" />
      </page-section>

      <!-- Products Section -->
      <page-section class="--no-padding-x --full-width flex --col --center" v-if="listVendorProducts && listVendorProducts.length > 0">
        <div class="heading --underline mb --center">Specialities at {{ getVendorInfo.vendor_name }}</div>
        <!-- <product-services :products="listVendorProducts" :services="getVendorInfo.services_offered" /> -->
        <slider-wrapper>
          <product-card :key="index" v-for="(product, index) in getSpecialityProducts" :productDetails="product"></product-card>
        </slider-wrapper>
        <view-all-drawer :list="listVendorProducts" />
      </page-section>

      <!-- Service Section -->
      <page-section class="--no-padding-x --full-width flex --col --center" v-if="getVendorInfo && getVendorInfo.services_offered.length > 0">
        <div class="heading --underline mb --center">Services Offered</div>
        <slider-wrapper>
          <div class="services-wrapper">
            <services-card :service="service" :key="index" v-for="(service, index) in getVendorInfo.services_offered" />
          </div>
        </slider-wrapper>
      </page-section>


      <!-- Gallery Section -->
      <page-section class="--full-width --no-padding-x" v-if="getVendorInfo.portfolio && getVendorInfo.portfolio.length > 0">
        <div class="heading --underline mb --center">The Gallery</div>
        <vendor-catalogue :imageList="getVendorInfo.portfolio" />
      </page-section>

      <!-- Upcoming Events Section -->
      <page-section class="--no-padding-x --full-width flex --col --center" v-if="getEventsInfo && getEventsInfo.length > 0">
        <div class="heading --underline mb --center">Events</div>
        <slider-wrapper>
          <event-card v-for="(event, index) in getEventsInfo" :key="index" :eventInfo="event" />
        </slider-wrapper>
      </page-section>

      <!-- Review Section -->
      <page-section class="--no-padding-x --no-padding-y --full-width" v-if="getVendorReviews && getVendorReviews.length > 0">
        <app-review bgImage="" :reviewList="getVendorReviews" :vendorId="getVendorInfo._id" />
      </page-section>

      <!-- Enquire Form -->
      <page-section class="--no-padding-x" id="contact-us">
        <div class="vendor-slug__enquire">
          <div class="vendor-slug__heading mr ml">
            <div class="heading --underline mb --center">Let's get in Touch</div>
            <div class="sub-heading description --l --center">Drop in your message and we will be glad to answer your queries.</div>
          </div>
          <contact-us :vendorInfo="getVendorInfo" />
        </div>
      </page-section>
    </div>

    <google-maps-card v-if="getVendorInfo && getVendorInfo.geolocation" :geolocation="getVendorInfo.geolocation" />
  </div>
</template>

<script>
import 'light-icons/dist/light-icon.css';
import vendorBanner from '@/components/vendor/VendorBanner.vue';
import PageSection from '@/components/PageSection.vue';
import AboutStore from '~/components/vendor/AboutStore.vue';
import StoreTabs from '@/components/vendor/StoreTabs.vue';
import ViewAllDrawer from '@/components/Drawer/ViewAllDrawer.vue';
import VendorCatalogue from '@/components/vendor/VendorCatalogue.vue';
import AppReviews from '@/components/AppReviews.vue';
import config from '@/config/index';
import EnquireForm from '@/components/vendor/EnquireForm.vue';
import VendorReviews from '@/components/vendor/VendorReviews.vue';
import AppReview from '~/components/AppReviews/AppReview.vue';
import { mapActions, mapGetters } from 'vuex';
import SliderWrapper from '~/components/SliderWrapper.vue';
import ProductCard from '~/components/vendor/ProductCard.vue';
import GoogleMapsCard from '~/components/GoogleMapsCard.vue';
import ContactUs from '~/components/ContactUs/ContactUs.vue';
import SocialHead from '~/components/SocialHead.vue';
import ProductServices from '~/components/vendor/ProductServices.vue';
import ServicesCard from '~/components/Services/ServicesCard.vue';
import FacilitiesCard from '~/components/Facilities/FacilitiesCard.vue';

export default {
  components: {
    vendorBanner,
    PageSection,
    AboutStore,
    StoreTabs,
    ViewAllDrawer,
    VendorCatalogue,
    AppReviews,
    EnquireForm,
    VendorReviews,
    AppReview,
    SliderWrapper,
    ProductCard,
    GoogleMapsCard,
    ContactUs,
    SocialHead,
    ProductServices,
    ServicesCard,
    FacilitiesCard,
  },
  async asyncData({ route, params, $axios, $config, req, store, from }) {
    const baseURL = config($config).API_BASE_PATH;
    try {
      const request1 = $axios.$get(`${baseURL}/v1/vendors/doc/${params.vendor_slug}?by=slug`);
      const request2 = $axios.$get(`${baseURL}/v1/products/list/${params.vendor_slug}?by=slug`);
      const request3 = $axios.$get(`${baseURL}/v1/reviews/list/vendor-reviews/${params.vendor_slug}?by=slug`);
      let [response, response2, response3] = await Promise.all([request1, request2, request3]);
      const response4 = await $axios.$get(`${baseURL}/v1/events/list/${response.vendorData._id}`);

      return {
        getVendorInfo: response.vendorData,
        listVendorProducts: response2.products,
        getVendorReviews: response3.reviews,
        getEventsInfo: response4.events,
      };
    } catch (error) {
      console.log(error);
      return {
        getVendorInfo: [],
        listVendorProducts: [],
        getVendorReviews: [],
        getEventsInfo: [],
      };
    }
  },
  methods: {
    ...mapActions('analytics', ['addAnalyticsItem']),
  },
  computed: {
    ...mapGetters('currentUser', ['currentUser']),
    getSpecialityProducts() {
      if (!this.listVendorProducts) return;
      const prodArray = [];
      for (let i = 0; i < this.listVendorProducts.length; i++) {
        this.listVendorProducts[i].is_speciality && prodArray.push(this.listVendorProducts[i]);
      }
      return prodArray;
    },
    getRatings() {
      if (this.getVendorReviews.length == 0) return;
      let totalRating = 0;
      this.getVendorReviews.forEach(review => {
        return (totalRating += review.rating);
      });
      return Math.round((totalRating / this.getVendorReviews.length) * 10) / 10;
    },
  },
  mounted() {
    console.log(this.getVendorInfo);
  },
};
</script>

<style lang="scss" scoped>
@import '@/assets/scss/global.scss';
.vendor-slug {
  .vendor-slug__sections {
    & > section:nth-child(even) {
      background-color: $color-bg-light;
    }
  }

  #enquire {
    min-height: calc(100vh - 56px);
    .vendor-slug__enquire {
      display: flex;
      flex-direction: column;
      align-items: center;
      justify-content: center;
    }
  }
}
</style>
