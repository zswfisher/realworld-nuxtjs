<template>
  <div class="editor-page">
    <div class="container page">
      <div class="row">
        <div class="col-md-10 offset-md-1 col-xs-12">
          <form @submit.prevent="onPublishArticle">
            <fieldset>
              <fieldset class="form-group">
                <input
                  v-model="formData.title"
                  type="text"
                  class="form-control form-control-lg"
                  placeholder="Article Title"
                  required
                />
              </fieldset>
              <fieldset class="form-group">
                <input
                  v-model="formData.description"
                  type="text"
                  class="form-control"
                  placeholder="What's this article about?"
                  required
                />
              </fieldset>
              <fieldset class="form-group">
                <textarea
                  v-model="formData.body"
                  class="form-control"
                  rows="8"
                  placeholder="Write your article (in markdown)"
                  required
                ></textarea>
              </fieldset>
              <fieldset class="form-group">
                <input
                  v-model="tag"
                  type="text"
                  class="form-control"
                  placeholder="Enter tags"
                  @keyup.enter="addTag(tag)"
                />
                <div class="tag-list">
                  <span
                    v-for="tag in formData.tagList"
                    :key="tag"
                    class="tag-default tag-pill ng-binding ng-scope"
                  >
                    <i class="ion-close-round" @click="removeTag(tag)"></i>
                    {{ tag }}
                  </span>
                </div>
              </fieldset>
              <button class="btn btn-lg pull-xs-right btn-primary">
                Publish Article
              </button>
            </fieldset>
          </form>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import { createArticle, updateArticle, getArticle } from "@/api/article";

export default {
  // 在路由匹配组件渲染之前，会先执行中间件
  middleware: "authenticated",
  name: "Editor",
  data() {
    return {
      tag: "",
      formData: {
        title: "",
        description: "",
        body: "",
        tagList: [],
      },
    };
  },
  async created() {
    const resp = this.$route.query.slug
      ? await getArticle(this.$route.query.slug)
      : undefined;
    if (resp) {
      const { article } = resp.data;
      this.formData = {
        title: article.title,
        description: article.description,
        body: article.body,
        tagList: article.tagList,
      };
    }
  },
  methods: {
    async onPublishArticle() {
      try {
        const slug = this.$route.query.slug;
        const resp = slug
          ? await updateArticle({ article: { article: this.formData}, slug: slug })
          : await createArticle({ article: this.formData });
        
        const { article } = resp.data;
        this.$router.push({
          name: "article",
          params: {
            slug: article.slug,
          },
        });
      } catch (error) {
        console.log(error);
      }
    },
    addTag(tag) {
      if (!this.formData.tagList.includes(tag)) {
        this.formData.tagList.push(tag);
      }
    },
    removeTag(tag) {
      const index = this.formData.tagList.findIndex((item) => item === tag);
      this.formData.tagList.splice(index, 1);
    },
  },
};
</script>

<style>
</style>