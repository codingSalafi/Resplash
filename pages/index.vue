<template>
  <div style>
    <div class="topBar">
      <h1 v-if="searching" class="searchTitle">Searching for: <span>"{{searchParam}}"</span></h1>
      <h1 v-if="found" class="searchTitle">Search Result for: <span>"{{searchParam}}"</span></h1>
      <div class="iconAndTextContainer">
        <div class="iconAndText" v-if="showIput">
          <svg style="width:24px;height:24px" viewBox="0 0 24 24">
            <path
              fill="currentColor"
              d="M9.5,3A6.5,6.5 0 0,1 16,9.5C16,11.11 15.41,12.59 14.44,13.73L14.71,14H15.5L20.5,19L19,20.5L14,15.5V14.71L13.73,14.44C12.59,15.41 11.11,16 9.5,16A6.5,6.5 0 0,1 3,9.5A6.5,6.5 0 0,1 9.5,3M9.5,5C7,5 5,7 5,9.5C5,12 7,14 9.5,14C12,14 14,12 14,9.5C14,7 12,5 9.5,5Z"
            />
          </svg>
        </div>
      </div>
      <input
        v-if="showIput"
        type="text"
        id="inputEl"
        class="searchInput"
        placeholder="Search for photo"
        v-model="searchParam"
      />
    </div>
    <div class="grid" v-if="loading">
      <div class="grid-item" v-for="(item, i) in 6" :key="i">
        <div class="captionLoading">
          <skeleton-loader width="100px" height="10px" bgcolor="#E6E6E6"></skeleton-loader>
          <br />
          <skeleton-loader style="float:left" width="70px" height="10px" bgcolor="#E6E6E6"></skeleton-loader>
        </div>
      </div>
    </div>
    <div class="grid" v-else>
      <div class="grid-item" @click="openDialog(photo)" v-for="(photo, i) in images" :key="i">
        <img class="grid-image" :src="photo.urls.thumb" alt />
        <div class="grid-image overlay">
          <div class="caption">
            <p class="title">
              <b>{{photo.user.name}}</b>
            </p>
            <p class="location">{{photo.user.location}}</p>
          </div>
        </div>
      </div>
    </div>
     
    <!--Modal-->
    <div class="pic_modal" data-pop="pop-in" @click="closeDialog()">
      <h3 class="close" @click="closeDialog()">&times;</h3>
      <div class="card" @click.stop v-if="openedImage">
        <div class="bigImageDiv">
          <img class="bigImage" :src="openedImage.urls.regular" alt />
        </div>
        <div class="bigCaption">
          <h3>{{openedImage.user.name}}</h3>
          <p>{{openedImage.user.location}}</p>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import SkeletonLoader from "../components/SkeletonLoader";
import axios from "axios";
export default {
  components: {
    SkeletonLoader
  },
  data() {
    return {
      loading: true,
      images: [],
      searchParam: "",
      openedImage: "",
      searching: false,
      found: false,
      showIput: true
    };
  },
  created() {
    var myThis = this;
    this.searchImages("African");
  },
  mounted() {
    var myThis = this;
    var searchInput = document.getElementById("inputEl");
    console.log("searchInput", searchInput == document.activeElement);
    searchInput.addEventListener("keyup", function(event) {
      if (event.keyCode === 13 && searchInput == document.activeElement) {
        myThis.showIput = false;
        myThis.searching = true;
        myThis.loading = true;
        myThis.searchImages(myThis.searchParam, "afterLoad");
      }
    });
  },
  methods: {
    openDialog(itm) {
      this.openedImage = itm;
      var theCard = document.querySelector(".card");
      var theModal = document.querySelector(".pic_modal");
      theModal.classList.add("show");
    },
    closeDialog() {
      var theCard = document.querySelector(".card");
      var theModal = document.querySelector(".pic_modal");
      theModal.classList.remove("show");
      theModal.classList.add("hide");
      this.openedImage = "";
    },
    searchImages(searchString, time) {
      var myThis = this;
      axios
        .get(
          `https://api.unsplash.com/search/photos?query=${searchString}&per_page=8`,
          {
            headers: {
              Authorization:
                "Client-ID X9_MJhmhnukqJwhuq1KhQbgAZzNPN-HsMrtODIf2SCA>",
              "Accept-Version": "v1"
            }
          }
        )
        .then(response => {
          myThis.images = response.data.results;
          myThis.loading = false;
          if (time == "afterLoad") {
            myThis.searching = false;
            myThis.found = true;
          }
          //console.log("The result is: ", myThis.images);
        })
        .catch(error => {
          myThis.images = [];
          console.log("The error is: ", error);
        });
    }
  }
};
</script>

<style lang="scss">
:root {
  --border-radius: 5px;
}
body {
  margin: 0px;
}
.topBar {
  width: 100%;
  background-color: #dde2e9;
  height: 200px;
  display: grid;
}
.searchInput {
  width: 80%;
  margin-left: auto;
  margin-right: auto;
  display: block;
  height: 40px;
  border: 0px solid black;
  border-radius: var(--border-radius);
  margin-top: 50px;
  padding-left: 70px;
}
.searchTitle {
    color: #344664;
    margin-top: 50px;
    padding-left: 70px;
    span{
      color:#717F94;
    }
  }

.iconAndTextContainer {
  left: 10%;
  display: block;
  position: absolute;
  height: 35px;
  margin-top: 60px;
}
.iconAndText {
  position: relative;
  padding-left: 10px;
  left: -50%;
}
.grid {
  margin-top: -70px;
  position: relative;
  display: grid;
  width: 70%;
  margin-right: auto;
  margin-left: auto;
  grid-template-columns: repeat(3, 1fr);
  grid-column-gap: 40px;
  background-color: transparent;
  min-height: 100vh;
  padding: 10px;
  justify-content: center;
  > div {
    background-color: #f5f5f5;
    text-align: center;
    padding: 20px 0;
    font-size: 30px;
    min-height: 100px;
  }
}
.grid-item {
  max-width: 100%;
  position: relative;
  padding: 0px;
  border-radius: var(--border-radius);
  cursor: pointer;
  &:nth-child(odd) {
    background: #f5f5f5;
    margin-bottom: 45px;
    /**height: 200px;*/
  }
  &:nth-child(even) {
    height: 250px;
    margin-top: -25px;
    margin-bottom: 20px;
  }
  &:nth-child(2) {
    margin-top: 0px !important;
  }
}
.grid-image {
  height: 100%;
  width: 100%;
  object-fit: cover;
  transition: transform 0.3s ease;
  position: absolute;
  top: 0;
  left: 0;
  border-radius: var(--border-radius);
}
.overlay {
  color: white;
  background: rgb(0, 0, 0);
  background: linear-gradient(
    0deg,
    rgba(0, 0, 0, 0.8939950980392157) 0%,
    rgba(6, 6, 15, 0.7511379551820728) 11%,
    rgba(255, 255, 255, 0) 100%
  );
}
.caption {
  position: absolute;
  bottom: 2px;
  padding-left: 20px;
}

.captionLoading {
  position: absolute;
  bottom: 15px;
  padding-left: 10px;
}
.title {
  font-size: 16px;
  text-align: left;
  padding: 0;
}
.location {
  font-size: 14px;
  text-align: left;
}
.pic_modal {
  visibility: hidden;
  position: fixed;
  opacity: 0;
  z-index: -1;
  padding-top: 50px;
  left: 0;
  top: 0;
  width: 100%;
  height: 100%;
  overflow: auto;
  background-color: rgba(0, 0, 0, 0.4);
  transition: all 1s ease;
}
.card {
  visibility: hidden;
  margin: auto;
  width: 50%;
  background-color: white;
  transform: scale(0);
  transition: all 0.5s ease-in-out;
  border-radius: var(--border-radius);
}
.bigImageDiv {
  height: 400px;
  padding: 0px;
}
.bigImage {
  height: 100%;
  width: 100%;
  object-fit: contain;
}
.bigCaption {
  width: 100%;
  padding: 10px;
  padding-left: 30px;
}
.bigText {
  font-size: 30px;
}
.bigP {
  font-size: 22px;
}
.close {
  font-size: 28px;
  position: absolute;
  top: 3px;
  right: 30px;
  color: white;
  cursor: pointer;
}
.pic_modal[data-pop="pop-in"].show {
  opacity: 1;
  visibility: visible;
  z-index: 100;
  > .card {
    transform: scale(1);
    visibility: visible;
    z-index: 200;
    opacity: 1;
  }

  
}
@media only screen and (max-width: 768px) {
  .grid {
    grid-template-columns: 100%;
    width: 80%;
  }
  .grid-item {
    &:nth-child(2n) {
      margin-bottom: 0px;
    }
    height: 200px !important;
    margin-top: 5px !important;
    margin-bottom: 18px !important;
  }
  .card {
    width: 85%;
  }
  .searchInput {
    width: 80%;

    padding-left: 40px;
  }
}
</style>
 