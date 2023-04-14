<template>
  <body>
    <div id="wrapper">
      <header>
        <img src="../assets/logo.png" class="img-fluid m-3" alt="" />
        <h2 class="mb-1">OpenAIに聞いてみよう〜</h2>
        <div class="btn-group m-3" role="group" aria-label="Basic example">
          <button
            type="button"
            class="btn"
            :class="
              screenMode === 'summary' ? 'btn-primary' : 'btn-outline-primary'
            "
            @click="onClickSummaryTab"
          >
            &emsp;梗概&emsp;
          </button>
          <button
            type="button"
            class="btn"
            :class="
              screenMode === 'title' ? 'btn-primary' : 'btn-outline-primary'
            "
            @click="onClickTitleTab"
          >
            タイトル
          </button>
          <button
            type="button"
            class="btn"
            :class="
              screenMode === 'opening' ? 'btn-primary' : 'btn-outline-primary'
            "
            @click="onClickOpeningTab"
          >
            書き出し
          </button>
        </div>
      </header>
      <div class="container">
        <div class="row">
          <div class="col-md-1"></div>
          <div class="col-md-10 text-center">
            <!-- 梗概 -->
            <div class="form-group" v-if="screenMode === 'summary'">
              <label class="form-label">作品の梗概を入力してね 。</label>
              <textarea
                class="form-control m-1"
                rows="3"
                v-model="userInput.summary"
                placeholder="1200文字まで"
              ></textarea>
              <button
                type="button"
                class="btn btn-outline-primary m-3"
                @click="askSummaryComment"
              >
                梗概にコメントしてもらおう〜
              </button>
              <loading
                v-model:active="isLoading"
                :can-cancel="false"
                :is-full-page="fullPage"
              />
              <p
                class="text-md-left"
                style="
                  text-align: left;
                  white-space: pre-wrap;
                  word-wrap: break-word;
                "
              >
                {{ answer.summary }}
              </p>
            </div>

            <!-- タイトル -->
            <div class="form-group" v-if="screenMode === 'title'">
              <label class="form-label">作品のタイトルを入力してね。</label>
              <textarea
                class="form-control m-1"
                rows="3"
                v-model="userInput.title"
                placeholder="50文字まで"
              ></textarea>
              <button
                type="button"
                class="btn btn-outline-primary m-3"
                @click="askTitleComment"
              >
                タイトルにコメントしてもらおう〜
              </button>
              <loading
                v-model:active="isLoading"
                :can-cancel="false"
                :is-full-page="fullPage"
              />
              <p
                class="text-md-left"
                style="
                  text-align: left;
                  white-space: pre-wrap;
                  word-wrap: break-word;
                "
              >
                {{ answer.title }}
              </p>
            </div>

            <!-- 実作の冒頭 -->
            <div class="form-group" v-if="screenMode === 'opening'">
              <label class="form-label">実作の冒頭を入力してね。</label>
              <textarea
                class="form-control m-1"
                rows="3"
                v-model="userInput.opening"
                placeholder="1200文字まで"
              ></textarea>
              <button
                type="button"
                class="btn btn-outline-primary m-3"
                @click="askOpeningComment"
              >
                実作の書き出しにコメントしてもらおう〜
              </button>
              <loading
                v-model:active="isLoading"
                :can-cancel="false"
                :is-full-page="fullPage"
              />
              <p
                class="text-md-left"
                style="
                  text-align: left;
                  white-space: pre-wrap;
                  word-wrap: break-word;
                "
              >
                {{ answer.opening }}
              </p>
            </div>
            <div class="col-md-1"></div>
          </div>
        </div>
      </div>
      <footer class="text-center text-lg-start">
        <div class="text-center p-4">
          <a class="text-reset" target="_blank" :href="`${links.twitter}`"
            ><small>©︎kkhaiya</small></a
          >
          <br />
          <a class="text-reset" target="_blank" :href="`${links.about}`"
            ><small>このサイトについて</small></a
          >
        </div>
      </footer>
    </div>
  </body>
</template>

<script lang="ts">
import { defineComponent } from "vue";
import Loading from "vue-loading-overlay";
import "vue-loading-overlay/dist/css/index.css";

export default defineComponent({
  data() {
    return {
      screenMode: "title",
      isLoading: false,
      fullPage: true,
      answer: {
        summary: "",
        title: "",
        opening: "",
      },
      params: {
        model: "gpt-3.5-turbo",
        temperature: 0.8,
        fetchURL: "https://api.openai.com/v1/chat/completions",
        summaryPromptMessage: [
          {
            role: "system",
            content:
              "以下の条件に従ってロールプレイせよ。あなたは凄腕の書評家・O氏であり、小説のプロットを読んでどのような感想や問題点を抱いたか出力する。決して小説を書いてはいけない。翻訳をしてもいけない。文末に「です」「ます」を使ってはいけない。#出力フォーマット :\n出力形式は以下のフォーマットとせよ。\n\n【ストーリーの独創性・面白さ】\n\n【設定・世界観の独創性・面白さ】\n\n【ストーリーを改善するには】\n\n【設定・世界観を改善するには】\n\n【キャラクターを改善するには】\n\n以下にプロット本文を提示する。\n\n&1",
          },
        ],
        // summaryPromptMessage: [{ role: "system", content: "good morning!" }],
        titlePromptMessage: [
          {
            role: "system",
            content:
              "以下の条件に従ってロールプレイせよ。あなたは熟練の編集者・K氏であり、小説のタイトルに対してどのような評価をしたか、代替案としてどのようなものが考えられるか出力する。決して小説を書いてはいけない。翻訳をしてもいけない。文末に「です」「ます」を使ってはいけない。#出力フォーマット :\n出力形式は以下のフォーマットとせよ。\n\n【タイトルの独創性・面白さ】\n\n【タイトルの美しさ・魅力】\n\n【他のタイトル案（5つ）】\n\nコメント対象のタイトルは以下の通り。「&1」\n\n",
          },
        ],
        openingPromptMessage: [
          {
            role: "system",
            content:
              "以下の条件に従ってロールプレイせよ。あなたは気鋭の批評家・A氏であり、小説の冒頭部分に対してどのような評価をしたか出力する。決して小説を書いてはいけない。翻訳をしてもいけない。文末に「です」「ます」を使ってはいけない。#出力フォーマット :\n出力形式は以下のフォーマットとせよ。\n\n【どのような魅力があるか】\n\n【どのような欠点があるか】\n\n【文体の特徴・魅力】\n\n【続きを読みたいと思うか】\n\n【その他の感想】\n\n以下に小説の冒頭部分を提示する。\n\n&1",
          },
        ],
      },
      userInput: {
        summary: "",
        title: "",
        opening: "",
      },
      message: {
        userNoInput: "入力してから訊いてね。",
        takingTime: "※わりと時間がかかります（最大数分くらい・・・）",
        error:
          "ごめんなさい、なんらかの問題が発生しました。もう一度試してみたら直るかもしれません。あるいは時間をおかないとダメかもしれません。",
      },
      links: {
        about:
          "https://skawano.notion.site/OpenAI-8929e1563a0147c19f3460c37d3c3e4f",
        twitter: "https://twitter.com/kkhaiya",
      },
    };
  },
  components: {
    Loading,
  },
  watch: {
    "userInput.summary": function (input) {
      this.userInput.summary = this.limitLength(input, 1200);
    },
    "userInput.title": function (input) {
      this.userInput.title = this.limitLength(input, 50);
    },
    "userInput.opening": function (input) {
      this.userInput.opening = this.limitLength(input, 1200);
    },
  },
  methods: {
    onClickSummaryTab() {
      this.screenMode = "summary";
    },
    onClickTitleTab() {
      this.screenMode = "title";
    },
    onClickOpeningTab() {
      this.screenMode = "opening";
    },
    limitLength(input: string, maxLength: number) {
      return input.length > maxLength ? input.slice(0, maxLength - 1) : input;
    },
    /**
     * プロンプトメッセージの編集
     * @param PromptMessage
     * @param userInput
     */
    editPromptMessage(PromptMessage: object[], userInput: string) {
      const PromptString = JSON.stringify(PromptMessage[0]);
      // プロンプトにユーザの入力値を挿入
      const editedPromptString1 = PromptString.replace("&1", userInput);
      // 改行コードを削除
      const editedPromptString2 = editedPromptString1.replace(/\r?\n/g, "");
      return [JSON.parse(editedPromptString2)];
    },
    /**
     * 梗概コメント
     */
    askSummaryComment() {
      if (!this.userInput.summary) {
        console.log("user has not input text");
        this.answer.summary = this.message.userNoInput;
        return;
      }
      this.answer.summary = this.message.takingTime;
      this.askGPT(
        "summary",
        this.params.model,
        this.editPromptMessage(
          this.params.summaryPromptMessage,
          this.userInput.summary
        ),
        this.params.temperature
      );
    },
    /**
     * タイトルコメント
     */
    askTitleComment() {
      if (!this.userInput.title) {
        console.log("user has not input text");
        this.answer.title = this.message.userNoInput;
        return;
      }
      this.answer.title = this.message.takingTime;
      this.askGPT(
        "title",
        this.params.model,
        this.editPromptMessage(
          this.params.titlePromptMessage,
          this.userInput.title
        ),
        this.params.temperature
      );
    },
    /**
     * 冒頭コメント
     */
    askOpeningComment() {
      if (!this.userInput.opening) {
        console.log("user has not input text");
        this.answer.opening = this.message.userNoInput;
        return;
      }
      this.answer.opening = this.message.takingTime;
      this.askGPT(
        "opening",
        this.params.model,
        this.editPromptMessage(
          this.params.openingPromptMessage,
          this.userInput.opening
        ),
        this.params.temperature
      );
    },
    /**
     * GPT汎用リクエスト
     */
    askGPT(
      type: string,
      model: string,
      messages: object[],
      temperature: number
    ) {
      this.isLoading = true;
      console.log("start querying...");
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
          stream: false,
          frequency_penalty: 0,
          presence_penalty: 0.5,
          stop: ['"""'],
        }),
      };
      fetch(this.params.fetchURL, requestOptions)
        .then((response) => response.json())
        .then((data) => {
          console.log("the answer is: " + JSON.stringify(data));
          let answerData = data["choices"][0]["message"]["content"];
          this.isLoading = false;
          //dataを更新
          switch (type) {
            case "summary":
              this.answer.summary = answerData;
              break;
            case "title":
              this.answer.title = answerData;
              break;
            case "opening":
              this.answer.opening = answerData;
              break;
            default:
              console.log("something is wrong in setting answer");
              return;
          }
          return answerData;
        })
        .catch((err) => {
          this.isLoading = false;
          console.log("the error message is: " + err);
          const errorMessage = this.message.error + "\ndetail: " + err;
          //dataを更新
          switch (type) {
            case "summary":
              this.answer.summary = errorMessage
              break;
            case "title":
              this.answer.title = errorMessage;
              break;
            case "opening":
              this.answer.opening = errorMessage;
              break;
            default:
              console.log("something is wrong in setting answer");
              return;
          }
          return err;
        });
    },
  },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h2 {
  color: #3f7a63;
}
body {
  color: #3f7a63;
}
#wrapper {
  background-color: #f29089;
}
.btn-primary {
  background: #3f7a63;
  border-color: #3f7a63;
  color: #f29089;
}
.btn-outline-primary {
  background: none;
  border-color: #3f7a63;
  color: #3f7a63;
}
.btn-primary:hover,
.btn-outline-primary:hover {
  background: #3f7a63;
  border-color: #3f7a63;
  color: #f29089;
}
.img-fluid {
  width: 100px;
}
</style>
