<template lang="pug">
  v-dialog(
    :content-class="$style.discoverModal" 
    v-model="isModalOpen" 
    width="360"
  )
    v-card(:class="$style.card")
      Icon(:class="$style.icon" :imageName="content.icon")
      h3 {{'Chapitre ' + content.id + ' - ' + content.name}}
      p(:class="$style.subtitle") {{content.name}}
      p(:class="$style.content") #[span Résumé :] {{content.abstract}}
      v-btn(:class="$style.btn" color="primary" :to="`/course/${content.apiId}`") Lire
      div(:class="$style.success" v-if="validate")
        img(src="/img/icons/success.svg")
        p Validé !
      div(:class="$style.favorite")
        img(src="/img/icons/fav.svg" v-if="favorite" @click="getFavorite(content.id)")
        svg(width="20" height="20" viewBox="0 0 20 20" fill="none" xmlns="http://www.w3.org/2000/svg" v-else @click="getFavorite(content.id)")
          path(d="M17.4798 7.2496L12.9284 6.55624L10.8942 2.24634C10.5297 1.47811 9.4736 1.46835 9.106 2.24634L7.07174 6.55624L2.52034 7.2496C1.70415 7.3733 1.37704 8.42473 1.96894 9.02694L5.26177 12.3798L4.48296 17.1161C4.34277 17.9723 5.2057 18.6135 5.92844 18.2131L10.0001 15.9768L14.0717 18.2131C14.7945 18.6103 15.6574 17.9723 15.5172 17.1161L14.7384 12.3798L18.0312 9.02694C18.6231 8.42473 18.296 7.3733 17.4798 7.2496ZM13.134 11.8329L13.8724 16.3381L10.0001 14.2125L6.12781 16.3381L6.86613 11.8329L3.73218 8.64283L8.06239 7.98528L10.0001 3.88371L11.9378 7.98528L16.268 8.64283L13.134 11.8329Z" fill="#545B67")
</template>

<script>
/* eslint-disable */
import Icon from "./components/Icon.vue"
export default {
  name: "DiscoverModal",
  components: {
    Icon
  },
  data() {
    return {
      validate: false,
      favorite: false
    }
  },
  props: {
    content: { type: Object, required: true },
    dialog: { type: Boolean, default: false }
  },
  updated() {
    if (this.localUser) {
      let localUser = this.localUser
      let favList = localUser.favorite_articles
      const whiteList = favList.map(fav => fav.id)
      if (whiteList.includes(this.content.id)) {
        this.favorite = true
      } else this.favorite = false
    }
  },
  methods: {
    setLocalUser(newLocaluser) {
      localStorage.setItem("userLog", JSON.stringify(newLocaluser))
    }/* ,
    async setBackUser({ favorite_articles }) {
      const idUser = this.$store.state.accountData.id
      const sendUser = await this.$http
        .put(`/user/${idUser}`, { favoriteArticles: favorite_articles })
        .then(reponse => console.log(response))
    } */,
    getFavorite(id) {
      this.favorite ? (this.favorite = false) : (this.favorite = true)
      let localUser = this.localUser
      let favList = localUser.favorite_articles
      const whiteList = favList.map(fav => fav.id) // Store all ID in the white list to use this list
      if (this.favorite && !whiteList.includes(id)) { // here for check if one of the element has been already stored
        favList.push(this.content)
        this.setLocalUser(localUser)
        /* this.setBackUser(localUser) */
      } else if (!this.favorite && whiteList.includes(id)) {
        favList = favList.filter(articleFav => articleFa.id !== id)
        localUser.favorite_articles = favList
        this.setLocalUser(localUser)
        /* this.setBackUser(localUser) */
      }
    }
  },
  computed: {
    localUser() {
      return JSON.parse(localStorage.getItem("userLog"))
    },
    isModalOpen: {
      get() {
        return this.dialog
      },
      set() {
        this.$emit("closeModal")
      }
    }
  }
}
</script>

<style lang="scss" module>
@import "@/scss/core/colors.scss";
.discoverModal {
  overflow: visible !important;
}
.card {
  padding: 16px !important;
  background-color: var(--v-gray-base) !important;
  text-align: center;
  font-family: "OpenSans", sans-serif;

  h3 {
    position: relative;
    font-size: 10px;
    margin: 0 !important;
  }
}
.icon {
  position: absolute;
  top: -25px;
  left: 50%;
  transform: translateX(-50%);
}
.subtitle {
  position: relative;
  font-family: "Gotham", sans-serif;
  font-size: 14px;
}
.content {
  font-size: 12px;

  span {
    font-weight: bold;
  }
}
.btn {
  color: $white !important;
}
.success {
  display: flex;
  align-items: center;
  position: absolute;
  top: 20px;

  img {
    margin-right: 8px;
  }

  p {
    font-family: "Gotham", sans-serif;
    font-size: 12px;
    margin: 0 !important;
    color: var(--v-success-base) !important;
  }
}
.favorite {
  position: absolute;
  top: 20px;
  right: 20px;
  cursor: pointer;

  img {
    width: 20px;
    height: 20px;
  }
  svg {
    path {
      fill: var(--v-text-base);
    }
  }
}
</style>
