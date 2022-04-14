<template>
  <img
    :alt="`${image.alt ? image.alt : 'Wonderland'}`"
    :width="width"
    :height="height"
    :src="placeholderSrc"
    data-sizes="auto"
    :data-srcset="srcSet"
    class="lazyload"
    decoding="async"
  />
</template>
<script>
import ImgixClient from "@imgix/js-core";
const client = new ImgixClient({
  domain: "www.datocms-assets.com",
});
export default {
  name: "ImageComp",
  props: {
    placeholderImage: {
      type: Boolean,
      default: false,
    },
    image: {
      type: Object,
      default: () => {
        return {};
      },
    },
    query: {
      type: Object,
      default: () => {
        return { auto: "format" };
      },
    },
    bgColor: {
      type: String,
      default: "grey",
    },
  },
  computed: {
    width() {
      return this.image.width;
    },
    height() {
      if (this.query.ar) {
        const ratioW = this.query.ar
          ? parseInt(this.query.ar.split(":")[0].replace(":", ""), 10)
          : 1;
        const ratioH = this.query.ar
          ? parseInt(this.query.ar.split(":")[1], 10)
          : 1;
        const mutliplierH = ratioW / ratioH;
        return this.width / mutliplierH;
      } else {
        return this.image.height;
      }
    },
    placeholderSrc() {
      if (this.placeholderImage) {
        return `${this.image.url}?w=100`;
      } else {
        return `data:image/svg+xml,%3Csvg%20xmlns=%22http://www.w3.org/2000/svg%22%20viewBox=%220%200%20${this.width}%20${this.height}%22%3E%3Crect%20width=%22${this.width}%22%20height=%22${this.height}%22%20style=%22fill:${this.bgColor}%22%3E%3C/rect%3E%3C/svg%3E`;
      }
    },
    srcSet() {
      return client.buildSrcSet(
        this.image.url.replace("https://www.datocms-assets.com", ""),
        this.query,
        { widths: [100, 300, 900, 1200, 1500, 1800] }
      );
    },
  },
};
</script>
