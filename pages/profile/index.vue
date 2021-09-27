<template>
  <div class="profile-page">
    <div class="user-info">
      <div class="container">
        <div class="row">
          <div class="col-xs-12 col-md-10 offset-md-1">
            <img :src="profile.image" class="user-img" />
            <h4>{{ profile.username }}</h4>
            <p>{{ profile.bio }}</p>
            <button
              class="btn btn-sm btn-outline-secondary action-btn"
              v-if="!isSelf"
            >
              <i class="ion-plus-round"></i>
              &nbsp; Follow {{ profile.username }}
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="container">
      <div class="row">
        <div class="col-xs-12 col-md-10 offset-md-1">
          <div class="articles-toggle">
            <ul class="nav nav-pills outline-active" v-if="profile.username">
              <li class="nav-item" v-if="isSelf">
                <nuxt-link
                  class="nav-link"
                  :class="{ active: $route.query.type === 'author' }"
                  exact
                  :to="{
                    name: 'profile',
                    params: {
                      username: profile.username,
                    },
                    query: {
                      type: 'author',
                    }
                  }"
                  >My Articles</nuxt-link
                >
              </li>
              
              <li class="nav-item">
                <nuxt-link
                  class="nav-link"
                  :class="{ active: $route.query.type !== 'author' }"
                  exact
                  :to="{
                    name: 'profile',
                    params: {
                      username: profile.username,
                    },
                    query: {
                      type: 'favorited',
                    }
                  }"
                  >Favorited Articles</nuxt-link
                >
              </li>
            </ul>
          </div>

          <div
            class="article-preview"
            v-for="item in articles"
            :key="item.slug"
          >
            <div class="article-meta">
              <a href=""><img :src="item.author.image" /></a>
              <div class="info">
                <a href="" class="author">{{ item.author.username }}</a>
                <span class="date">{{ item.createdAt | date('MMM DD, YYYY')}}</span>
              </div>
              <button 
                class="btn btn-outline-primary btn-sm pull-xs-right"
                :class="{ active: item.favorited }"
              >
                <i class="ion-heart"></i> {{ item.favoritesCount }}
              </button>
            </div>
            <nuxt-link 
              class="preview-link"
              :to="{
                name: 'article',
                params: {
                  slug: item.slug,
                },
              }"
            >
              <h1>
                {{ item.title }}
              </h1>
              <p>{{ item.description }}</p>
              <span>Read more...</span>
              <ul class="tag-list" v-for="tag in item.tagList" :key="tag">
                <li class="tag-default tag-pill tag-outline">{{ tag }}</li>
              </ul>
            </nuxt-link>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { getProfile } from "@/api/user";
import { getArticles } from "@/api/article";
import { mapState } from "vuex";

export default {
  // 在路由匹配组件渲染之前，会先执行中间件
  middleware: "authenticated",
  name: "UserProfile",
  data() {
    return {
      profile: {},
      articles: [],
    };
  },
  computed: {
    ...mapState(["user"]),
    isSelf () {
      return this.user.username === this.profile.username;
    }
  },
  watchQuery: ["type"],
  async asyncData({ params, query }) {
    const resp = await getProfile(params.username);
    const profile = resp.data.profile;
    // 获取文章信息
    const articleReap = query.type === 'author'
      ? await getArticles({ author: profile.username })
      : await getArticles({ favorited: profile.username });
    return {
      profile,
      articles: articleReap.data.articles,
    }
  },
};
</script>

<style>
</style>