<template>
  <div class="sidebar"
       :data="backgroundColor">
    <!--
            Tip 1: you can change the color of the sidebar's background using: data-background-color="white | black | darkblue"
            Tip 2: you can change the color of the active button using the data-active-color="primary | info | success | warning | danger"
        -->
    <!-- -->
    <div class="sidebar-wrapper" id="style-3">
      <div class="logo">
        <a href=""
           aria-label="sidebar mini logo"
           class="simple-text logo-mini">
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-dice-1" viewBox="0 0 16 16">
            <circle cx="8" cy="8" r="1.5"/>
            <path d="M13 1a2 2 0 0 1 2 2v10a2 2 0 0 1-2 2H3a2 2 0 0 1-2-2V3a2 2 0 0 1 2-2h10zM3 0a3 3 0 0 0-3 3v10a3 3 0 0 0 3 3h10a3 3 0 0 0 3-3V3a3 3 0 0 0-3-3H3z"/>
          </svg>
        </a>
        <a href="" class="simple-text logo-normal">
          <svg xmlns="http://www.w3.org/2000/svg" width="16" height="16" fill="currentColor" class="bi bi-dice-3" viewBox="0 0 16 16">
            <path d="M13 1a2 2 0 0 1 2 2v10a2 2 0 0 1-2 2H3a2 2 0 0 1-2-2V3a2 2 0 0 1 2-2h10zM3 0a3 3 0 0 0-3 3v10a3 3 0 0 0 3 3h10a3 3 0 0 0 3-3V3a3 3 0 0 0-3-3H3z"/>
            <path d="M5.5 4a1.5 1.5 0 1 1-3 0 1.5 1.5 0 0 1 3 0zm8 8a1.5 1.5 0 1 1-3 0 1.5 1.5 0 0 1 3 0zm-4-4a1.5 1.5 0 1 1-3 0 1.5 1.5 0 0 1 3 0z"/>
          </svg>
        </a>
      </div>
      <slot>

      </slot>
      <ul class="nav">
        <!--By default vue-router adds an active class to each route link. This way the links are colored when clicked-->
        <slot name="links">
          <sidebar-link v-for="(link,index) in sidebarLinks"
                        :key="index"
                        :to="link.path"
                        :name="link.name"
                        :icon="link.icon">
          </sidebar-link>
        </slot>
      </ul>
    </div>
  </div>
</template>
<script>
  import SidebarLink from "./SidebarLink";

  export default {
    props: {
      title: {
        type: String,
        default: "Creative Tim"
      },
      backgroundColor: {
        type: String,
        default: "vue"
      },
      activeColor: {
        type: String,
        default: "success",
        validator: value => {
          let acceptedValues = [
            "primary",
            "info",
            "success",
            "warning",
            "danger"
          ];
          return acceptedValues.indexOf(value) !== -1;
        }
      },
      sidebarLinks: {
        type: Array,
        default: () => []
      },
      autoClose: {
        type: Boolean,
        default: true
      }
    },
    provide() {
      return {
        autoClose: this.autoClose,
        addLink: this.addLink,
        removeLink: this.removeLink
      };
    },
    components: {
      SidebarLink
    },
    computed: {
      /**
       * Styles to animate the arrow near the current active sidebar link
       * @returns {{transform: string}}
       */
      arrowMovePx() {
        return this.linkHeight * this.activeLinkIndex;
      },
      shortTitle() {
        return this.title.split(' ')
          .map(word => word.charAt(0))
          .join('').toUpperCase();
      }
    },
    data() {
      return {
        linkHeight: 65,
        activeLinkIndex: 0,
        windowWidth: 0,
        isWindows: false,
        hasAutoHeight: false,
        links: []
      };
    },
    methods: {
      findActiveLink() {
        this.links.forEach((link, index) => {
          if (link.isActive()) {
            this.activeLinkIndex = index;
          }
        });
      },
      addLink(link) {
        const index = this.$slots.links.indexOf(link.$vnode);
        this.links.splice(index, 0, link);
      },
      removeLink(link) {
        const index = this.links.indexOf(link);
        if (index > -1) {
          this.links.splice(index, 1);
        }
      }
    },
    mounted() {
      this.$watch("$route", this.findActiveLink, {
        immediate: true
      });
    }
  };
</script>
