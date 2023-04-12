<template>
  <div>
    <header>
      <h1 class="mb-3">Genron SF - ask to GPT</h1>
    </header>
    <br />
    <div class="mb-3 form-group">
      <div class="container-fluid">
        <div class="row">
          <div class="col-md-1"></div>
          <div class="col-md-10">
            <form>
              <div class="form-group">
                <label for="exampleFormControlInput1" class="form-label"
                  >Input your Genron SF work URL</label
                >
                <input
                  type="url"
                  class="form-control"
                  id="exampleFormControlInput1"
                  placeholder="https://school.genron.co.jp/works/sf/2020/students/skawano/4486/"
                />
              </div>
              <br />
              <button type="button" class="btn btn-outline-primary">
                Start!
              </button>
            </form>
          </div>
          <div class="col-md-1"></div>
        </div>
      </div>
    </div>
    <div class="mb-3 form-group">
      <div class="container-fluid">
        <div class="row">
          <div class="col-md-1"></div>
          <div class="col-md-10">
            <form>
              <div class="form-group">
                <label for="exampleFormControlInput1" class="form-label">
                  タイトルへのコメント
                </label>
                <textarea
                  class="form-control"
                  id="exampleFormControlTextarea1"
                  rows="3"
                ></textarea>
              </div>
            </form>
            <br />
            <form>
              <div class="form-group">
                <label for="exampleFormControlInput1" class="form-label">
                  梗概へのコメント
                </label>
                <textarea
                  class="form-control"
                  id="exampleFormControlTextarea1"
                  rows="3"
                ></textarea>
              </div>
            </form>
            <br />
            <form>
              <div class="form-group">
                <label for="exampleFormControlInput1" class="form-label">
                  実作の冒頭部へのコメント
                </label>
                <textarea
                  class="form-control"
                  id="exampleFormControlTextarea1"
                  rows="3"
                ></textarea>
              </div>
            </form>
          </div>
          <div class="col-md-1"></div>
        </div>
      </div>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

export default defineComponent({
  setup() {
    
    let titleComment = "";
    
    const askTitleComment = () => titleComment = "";

    return {
        titleComment,
        askTitleComment,
    };
  },
});
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
