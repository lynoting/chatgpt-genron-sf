<template>
  <body>
    <div>
      <header>
        <h1 class="mb-1">Genron SF - ask to GPT</h1>
        <div class="btn-group m-3" role="group" aria-label="Basic example">
          <button
            type="button"
            class="btn btn-sm"
            :class="
              screenMode === 'summary' ? 'btn-primary' : 'btn-outline-primary'
            "
            @click="onClickSummaryTab"
          >
            梗概
          </button>
          <button
            type="button"
            class="btn btn-sm"
            :class="
              screenMode === 'title' ? 'btn-primary' : 'btn-outline-primary'
            "
            @click="onClickTitleTab"
          >
            タイトル
          </button>
          <button
            type="button"
            class="btn btn-sm"
            :class="
              screenMode === 'opening' ? 'btn-primary' : 'btn-outline-primary'
            "
            @click="onClickOpeningTab"
          >
            書き出し
          </button>
        </div>
      </header>
      <div class="container-fluid">
        <div class="row">
          <form>
            <div class="mb-3 form-group">
              <div class="col-md-1"></div>
              <div class="col-md-10">
                <!-- 梗概 -->
                <div class="form-group" v-if="screenMode === 'summary'">
                  <label for="exampleFormControlInput1" class="form-label"
                    >作品の梗概を入力してね（1200文字まで） 。</label
                  >
                  <textarea
                    class="form-control m-1"
                    id="exampleFormControlTextarea1"
                    rows="3"
                    v-model="userInput.summary"
                  ></textarea>
                  <button
                    type="button"
                    class="btn btn-outline-primary btn-sm m-1"
                    @click="askSummaryComment"
                  >
                    梗概にコメントしてもらおう〜
                  </button>
                  <p
                    class="text-md-left newline"
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
                  <label for="exampleFormControlInput1" class="form-label"
                    >作品のタイトルを入力してね。</label
                  >
                  <textarea
                    class="form-control m-1"
                    id="exampleFormControlTextarea1"
                    rows="3"
                    v-model="userInput.title"
                  ></textarea>
                  <button
                    type="button"
                    class="btn btn-outline-primary btn-sm m-1"
                    @click="askTitleComment"
                  >
                    タイトルにコメントしてもらおう〜
                  </button>
                  <p
                    class="text-md-left newline"
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
                  <label for="exampleFormControlInput1" class="form-label"
                    >実作の冒頭を入力してね（1200文字まで）。</label
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
                  <p
                    class="text-md-left newline"
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
          </form>
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
      answer: {
        summary: "",
        title: "",
        opening: "",
      },
      params: {
        model: "gpt-3.5-turbo",
        temperature: 0.8,
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
        return;
      }
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
        console.log(this.userInput.title);
        console.log("user has not input text");
        return;
      }
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
      if (!this.userInput.summary) {
        console.log("user has not input text");
        return;
      }
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
          stream: false, //If set, partial message deltas will be sent, like in ChatGPT.
          frequency_penalty: 0,
          presence_penalty: 0.5,
          stop: ['"""'],
        }),
      };
      fetch("https://api.openai.com/v1/chat/completions", requestOptions)
        .then((response) => response.json())
        .then((data) => {
          console.log("the answer is: " + JSON.stringify(data));
          let answerData = data["choices"][0]["message"]["content"];
          console.log("the answer is: " + answerData);
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
/* p.new-line {
  white-space: pre-wrap;
  word-wrap: break-word;
  text-align: left;
} */
</style>
