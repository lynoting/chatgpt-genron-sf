<template>
  <body>
    <div>
      <header>
        <h1 class="mb-1">Genron SF - ask to GPT</h1>
        <div class="btn-group m-3" role="group" aria-label="Basic example">
          <button type="button" class="btn btn-sm btn-outline-primary" @click="onClickSummaryTab">
            梗概
          </button>
          <button type="button" class="btn btn-sm btn-outline-primary" @click="onClickTitleTab">
            タイトル
          </button>
          <button type="button" class="btn btn-sm btn-outline-primary" @click="onClickOpeningTab">
            書き出し
          </button>
        </div>
      </header>
      <div class="mb-3 form-group">
        <div class="container-fluid">
          <div class="row">
            <div class="col-md-1"></div>
            <div class="col-md-10">
              <form>
                <!-- 梗概 -->
                <div class="form-group" v-if="screenMode === 'summary'">
                  <label for="exampleFormControlInput1" class="form-label"
                    >作品の梗概を1500文字以内で入力してね。</label
                  >
                  <textarea
                    class="form-control m-1"
                    id="exampleFormControlTextarea1"
                    rows="3"
                  ></textarea>
                  <button
                    type="button"
                    class="btn btn-outline-primary btn-sm m-1"
                    @click="askSummaryComment"
                  >
                    梗概にコメントしてもらおう〜
                  </button>
                <p>
                  {{ answer }}
                </p>
              </div>
                <!-- タイトル -->
                <div class="form-group" v-if="screenMode === 'title'">
                  <label for="exampleFormControlInput1" class="form-label"
                    >作品のタイトルを入力してね。</label
                  >
                  <textarea
                    class="form-control m-1"
                    id="exampleFormControlTextarea1"
                    rows="3"
                    v-model="answer"
                  ></textarea>
                  <button
                    type="button"
                    class="btn btn-outline-primary btn-sm m-1"
                    @click="askTitleComment"
                  >
                    タイトルにコメントしてもらおう〜
                  </button>
                  <p>
                  {{ answer }}
                  </p>
                </div>

                <!-- 実作の冒頭 -->

                <div class="form-group" v-if="screenMode === 'opening'">
                  <label for="exampleFormControlInput1" class="form-label"
                    >実作の冒頭を入力してね。</label
                  >
                  <textarea
                    class="form-control m-1"
                    id="exampleFormControlTextarea1"
                    rows="3"
                  ></textarea>
                  <button
                    type="button"
                    class="btn btn-outline-primary btn-sm m-1"
                    @click="askOpeningComment"
                  >
                    実作の書き出しにコメントしてもらおう〜
                  </button>
                  <p>
                  {{ answer }}
                </p>
                </div>
              </form>
            </div>
            <div class="col-md-1"></div>
          </div>
        </div>
      </div>
    </div>
  </body>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  data() {
    return {
      screenMode: "summary",
      answer: "",
      todos: [],
      params: {
        model: "gpt-3.5-turbo",
        temperature: 0.8,
        titleCommentMessage: [{ role: "user", content: "Hello!" }],
        summaryCommentMessage: [{ role: "user", content: "Hello!" }],
        openingCommentMessage: [{ role: "user", content: "Hello!" }],
      },
      url: "https://school.genron.co.jp/works/sf/2020/students/skawano/4486/",
    };
  },

  methods: {

    /**
     * タブ切り替え
     */
    onClickSummaryTab() {
      this.screenMode = "summary";
    },
    onClickTitleTab() {
      this.screenMode = "title";
    },
    onClickOpeningTab() {
      this.screenMode = "opening";
    },

    updateData() {
      this.answer = "";
    },
    /**
     * タイトルコメント
     */
    askTitleComment() {
      this.askGPT(
        this.params.model,
        this.params.titleCommentMessage,
        this.params.temperature
      );
    },
    /**
     * 梗概コメント
     */
    askSummaryComment() {
      this.askGPT(
        this.params.model,
        this.params.summaryCommentMessage,
        this.params.temperature
      );
    },
    /**
     * 冒頭コメント
     */
    askOpeningComment() {
      this.askGPT(
        this.params.model,
        this.params.openingCommentMessage,
        this.params.temperature
      );
    },
    /**
     * GPT汎用リクエスト
     */
    askGPT(model: string, messages: object[], temperature: number) {
      console.log("start querying...")
      const requestOptions = {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
          Authorization: "Bearer " + String(process.env.VUE_APP_OPENAI_API_KEY),
        },
        body: JSON.stringify({
          model: model,
          messages: messages,
          temperature: temperature,
          top_p: 1,
          n: 1,
          stream: false, //If set, partial message deltas will be sent, like in ChatGPT.
          frequency_penalty: 0,
          presence_penalty: 0.5,
          stop: ['"""'],
        }),
      };
      fetch("https://api.openai.com/v1/chat/completions", requestOptions)
        .then((response) => response.json())
        .then((data) => {
          let ans = data["choices"][0]["message"]["content"];
          console.log("the answer is: " + ans);
          this.answer = ans;
          return ans;
        })
        .catch((err) => {
          console.log("the error message is: " + err);
          return err;
        });
    },
  },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
header {
}
h1 {
  color: #48c1ec;
}
body {
}
</style>
