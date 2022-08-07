<template>
  <v-row class="main" justify="center" align="center" align-self="center">
    <v-col lg="3" md="11">
       <!-- Uyarılar -->
       <v-alert
      dense
      outlined
      type="warning"
      v-if="alert"
    >
     Mesaj alanı boş bırakılamaz
    </v-alert>
      <v-alert
      dense
      outlined
      type="success"
      v-if="deleted"
    >
     Mesaj silindi
    </v-alert>
    <!-- mesaj ekleme  -->
      <div class="create-line">
        <v-textarea
          elevation="0"
          solo
          name="input-7-4"
          label="Your text here"
          v-model="item.message"
        ></v-textarea>
        <div class="actions d-flex justify-space-between">
          <div class="attach">
            <v-btn text small>
              <img src="~/assets/icons/attach.svg" alt="" srcset="" />
            </v-btn>
            <v-btn text small>
              <img src="~/assets/icons/smile.svg" alt="" srcset="" />
            </v-btn>
          </div>
          <div class="submit">
            <v-btn dark color="purple"  @click="addItem()">submit</v-btn>
          </div>
        </div>
      </div>
      <!-- mesajların listenmesi -->
      <div class="list-line">
        <div
          class="list-item mt-1"
          v-for="(item, index) in listItem"
          :key="index"
        >
          <div class="item-show overflow-hidden">
            <div class="items d-flex">
              <div class="item-image">
                <img src="~/assets/icons/user.png" height="40px" alt="" />
              </div>
              <div class="detail">
                <div class="item-date">
                  {{ item.date | dateFormat }}
                </div>
                <div class="item-message">
                  <textarea
                    type="text"
                    disabled
                    class="show-input"
                    v-model="item.message"
                  >
                  </textarea>
                </div>
              </div>
            </div>

            <div class="update-line">
              <div class="buttons">
                <v-btn small text @click="cancel(index)">cancel</v-btn>
                <v-btn dark color="purple" small @click="updateItem(index)"
                  >Update</v-btn
                >
              </div>

              <div class="item-actions d-flex justify-space-between">
                <div class="like-actions">
                  <v-btn text small>
                    <img
                      v-if="!item.like"
                      src="~/assets/icons/like.svg"
                      height="16px"
                      @click="liked(index)"
                    />
                    <img
                      v-if="item.like"
                      src="~/assets/icons/liked.svg"
                      height="16px"
                      @click="liked(index)"
                    />
                  </v-btn>
                  <v-btn text small>
                    <img
                      v-if="!item.disslike"
                      src="~/assets/icons/disslike.svg"
                      height="16px"
                      @click="disslike(index)"
                    />
                    <img
                      v-if="item.disslike"
                      src="~/assets/icons/dissliked.svg"
                      height="16px"
                      @click="disslike(index)"
                    />
                  </v-btn>
                </div>
                <div class="edit-actions">
                  <v-btn text small>
                    <img
                      src="~/assets/icons/edit.svg"
                      height="16px"
                      @click="editedItem(index)"
                    />
                  </v-btn>
                  <v-btn text small>
                    <img
                      src="~/assets/icons/delete.svg"
                      height="16px"
                      @click="deletedItem(index)"
                    />
                  </v-btn>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
    </v-col>
  </v-row>
</template>

<script>

export default {

  name: "IndexPage",
  mounted() {
    this.getData();
  },

  data() {
    return {
      item: {
        message: null,
        date: null,
        like: false,
        disslike: false,
      },
      listItem: [],
      alert:false,
      deleted:false
    };
  },
  methods: {
    getData() {
      this.listItem = JSON.parse(localStorage.getItem("messages")) ?? [];
    },
    addItem() {
      if (this.item.message) {
        this.item.date = new Date();
        this.listItem.push(this.item);
        localStorage.setItem("messages", JSON.stringify(this.listItem));
        this.item = { message: null, date: null };
      }
      else{
        this.alert = true
        setTimeout(() => {
            this.alert = false
        }, 1500);
      }
    },
    deletedItem(index) {
      this.listItem.splice(index, 1);
      localStorage.setItem("messages", JSON.stringify(this.listItem));
       this.deleted = true
        setTimeout(() => {
            this.deleted = false
        }, 1500);
    },
    editedItem(index) {
      const input = document.querySelectorAll(".show-input")[index];
      this.updateToggle(index);
      input.removeAttribute("disabled");
    },
    updateItem(index) {
      const input = document.querySelectorAll(".show-input")[index];
      localStorage.setItem("messages", JSON.stringify(this.listItem));
      this.updateToggle(index);
      input.setAttribute("disabled", "enabled");
    },
    cancel(index) {
      this.updateToggle(index);
    },
    updateToggle(index) {
      const input = document.querySelectorAll(".show-input")[index];
      const icons = document.querySelectorAll(".item-actions")[index];
      const buttons = document.querySelectorAll(".buttons")[index];
      const updateline = document.querySelectorAll(".update-line")[index];
      input.setAttribute("disabled", "enabled");
      updateline.classList.toggle("active");
      buttons.classList.toggle("active");
      icons.classList.toggle("active");
    },
    liked(index) {
      this.listItem[index].like = !this.listItem[index].like;
      if (this.listItem[index].disslike) {
        this.listItem[index].disslike = !this.listItem[index].disslike;
      }
      localStorage.setItem("messages", JSON.stringify(this.listItem));
    },
    disslike(index) {
      this.listItem[index].disslike = !this.listItem[index].disslike;
      if (this.listItem[index].like) {
        this.listItem[index].like = !this.listItem[index].like;
      }
      localStorage.setItem("messages", JSON.stringify(this.listItem));
    },
  },
  filters: {
    dateFormat(val) {
      return new Date(val).toLocaleString();
    },
  },
};
</script>
<style lang="scss">
</style>

<style scoped>
.main {
  background: #34495e;
  width: 100vw;
  padding: 20px 0;
}
.create-line {
  background: #fff;
  padding: 15px 30px;
  border-radius: 5px;
  margin-bottom: 20px;
}
.list-item {
  background: #fff;
  height: 130px;
  border-radius: 5px;
  padding: 20px;
  font-size: 14px;
  margin-bottom: 10px;
  transition: all 0.3s ease;
  position: relative;
}
.detail {
  overflow: hidden;
  width: 100%;
}
.item-show {
  margin-bottom: 10px;
  height: 55px;
  transition: all 0.3s ease;
}
.items {
  margin-bottom: 10px;
  height: 55px;
  transition: all 0.3s ease;
}
.item-actions {
  border-top: 1px solid #c1c8ce;
  padding-top: 10px;
}
.item-image {
  margin-right: 10px;
}
.item-date {
  font-size: 8px;
  color: #99a4ae;
}
.item-message {
  color: #5d6d7e;
  height: 30px;
  transition: all 0.3s ease;
}
.show-input {
  width: 100%;
  word-wrap: break-word;
  height: 55px;
  transition: all 0.3s ease;
}
.show-input:focus-visible {
  outline: none;
}
.update-line {
  position: absolute;
  right: 0;
  background: #ffff;
  z-index: 12;
  width: 100%;
  left: 0;
  display: block;
}
.item-actions.active {
  box-shadow: 0 3px 6px rgba(0, 0, 0, 0.1);
  padding-bottom: 5px;
}
.buttons {
  display: none;
  padding-bottom: 5px;
}
.buttons.active {
  display: flex;
  justify-content: flex-end;
  margin-right: 10px;
}
.update-line.active {
  padding-top: 30px;
}
</style>
<style>
.v-input__slot {
  box-shadow: none !important;
}
</style>