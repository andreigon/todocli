<template>
  <div id="app">
    <div class="header">
      <div class="container">
        <div class="logo">VUE ToDo List</div>
        <div class="form">
          <input
            type="text"
            v-model="valueInput"
            v-on:keypress.enter="addTask"
          />
          <button class="btn" v-on:click="addTask">Сделаем</button>
        </div>
      </div>
    </div>
    <div class="container">
      <h2>
        <span>Мои Дела</span>
        <span class="mask-num">{{ needDoList.length }}</span>
      </h2>
      <ul class="mask-list">
        <li v-for="(mask, index) in needDoList" :key="mask.id">
          <div>
            <input type="checkbox" v-on:change="doCheck(index, 'need')" />
            <span>{{ mask.title }}</span>
          </div>
          <button class="btn-remove" v-on:click="removeMask(index, 'need')">
            Сделал
          </button>
        </li>
      </ul>
      <h2>
        <span>Сделано</span>
        <span class="mask-num">{{ completelist.length }}</span>
      </h2>
      <ul class="mask-list complete-list">
        <li v-for="(mask, index) in completelist" :key="mask.id">
          <div>
            <input
              type="checkbox"
              v-on:change="doCheck(index, 'complete')"
              checked
            />
            <span>{{ mask.title }}</span>
          </div>
          <button class="btn-remove" v-on:click="removeMask(index, 'complete')">
            Сделал
          </button>
        </li>
      </ul>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      valueInput: "",
      needDoList: [],
      completelist: [],
    };
  },

  mounted() {
    if (localStorage.getItem("needDoList")) {
      try {
        this.needDoList = JSON.parse(localStorage.getItem("needDoList"));
      } catch (e) {
        localStorage.removeItem("needDoList");
      }
    }

    if (localStorage.getItem("completelist")) {
      try {
        this.completelist = JSON.parse(localStorage.getItem("completelist"));
      } catch (e) {
        localStorage.removeItem("completelist");
      }
    }
  },

  methods: {
    addTask() {
      if (!this.valueInput) {
        return;
      }
      this.needDoList.push({
        title: this.valueInput,
        id: Math.random(),
      });
      this.valueInput = "";
      this.saveTasks();
    },
    doCheck(index, type) {
      if (type === "need") {
        const completeMask = this.needDoList.splice(index, 1);
        this.completelist.push(...completeMask);
      } else {
        const noCompleteMask = this.completelist.splice(index, 1);
        this.needDoList.push(...noCompleteMask);
      }
      this.saveTasks();
      this.saveCompleteTask();
    },
    removeMask(index, type) {
      const toDoList = type === "need" ? this.needDoList : this.completelist;
      toDoList.splice(index, 1);
      this.saveTasks();
    },
    saveTasks() {
      const parsed = JSON.stringify(this.needDoList);
      localStorage.setItem("needDoList", parsed);
    },
    saveCompleteTask() {
      const parsed = JSON.stringify(this.completelist);
      localStorage.setItem("completelist", parsed);
    },
  },
};
</script>

<style lang="scss">
body {
  font-family: "Ubuntu", sans-serif;
  font-weight: 300;
}

h2 {
  display: flex;
  align-items: center;
  justify-content: space-between;
  padding-bottom: 20px;
  margin: 25px 0;
  border-bottom: 1px solid #bfbfbf;
}

h2 .mask-num {
  border-radius: 20px;
  border: 2px solid #2a2f36;
  color: #2a2f36;
  font-size: 14px;
  font-weight: 700;
  padding: 5px 10px;
}

.container {
  width: 800px;
  margin: 0 auto;
  padding: 0 15px;
}

.header {
  background-color: #333;
  padding: 10px 0;
  margin-bottom: 40px;
}

.header .container {
  display: flex;
  align-items: center;
  justify-content: space-between;
}

.header .container .logo {
  width: 100px;
  line-height: 50px;
  color: #ddd;
  font-size: 24px;
  cursor: pointer;
}

.header .container .form {
  width: 60%;
  display: flex;
  justify-content: space-between;
}

.header .container .form input {
  width: calc(100% - 145px);
  height: 40px;
  text-indent: 10px;
  border: 1px solid #fff;
  border-radius: 5px;
  border-radius: 0;
  box-sizing: border-box;
  outline: none;
  transition: 0.2s border;
}

.header .container .form input:focus {
  border: 1px solid #12c0dd;
}

.btn {
  background-color: #12c0dd;
  border: 1px solid #12c0dd;
  color: #fff;
  font-weight: 700;
  text-transform: uppercase;
  padding: 10px 15px;
  font-size: 12px;
  height: 40px;
  outline: none;
  transition: 0.2s;
}

.btn:hover {
  background-color: #0dd6f8;
  border-color: #0dd6f8;
}

.mask-list li {
  display: flex;
  align-items: center;
  justify-content: space-between;
  height: 32px;
  line-height: 32px;
  margin-bottom: 10px;
  border-radius: 3px;
  background-color: #fff;
}

.mask-list.complete-list li {
  border-left-color: #999;
  opacity: 0.5;
}
.mask-list {
  padding-left: 0;
}

input[type="checkbox"] + span {
  padding-left: 10px;
}

.btn-remove {
  border: 1px solid #2a2f36;
  font-size: 12px;
  border: 2px solid #2a2f36;
  background-color: #333333;
  color: #fff;
  font-weight: 700;
  text-transform: uppercase;
  padding: 5px 10px;
  font-size: 12px;
  outline: none;
  transition: 0.2s;
}

.btn-remove:hover {
  background-color: #535151;
  border-color: #535151;
}

input[type="checkbox"]:checked,
input[type="checkbox"]:not(:checked) {
  position: absolute;
  z-index: 1;
  opacity: 0;
  width: 20px;
  height: 20px;
  margin-top: 6px;
}
input[type="checkbox"]:checked + span,
input[type="checkbox"]:not(:checked) + span {
  position: relative;
  padding-left: 35px;
  cursor: pointer;
}
input[type="checkbox"]:checked + span::before {
  content: "";
  position: absolute;
  width: 16px;
  height: 16px;
  left: 0;
  top: calc(50% - 10px);
  background-color: transparent;
  border-radius: 2px;
  border: 2px solid #4fc1de;
}
input[type="checkbox"]:checked + span::after {
  content: "";
  position: absolute;
  width: 12px;
  height: 12px;
  transition: all 0.2s ease;
  opacity: 1;
  left: 4px;
  top: calc(50% - 6px);
  background-color: #4fc1de;
}
input[type="checkbox"]:not(:checked) + span::before {
  content: "";
  position: absolute;
  width: 16px;
  height: 16px;
  left: 0;
  top: calc(50% - 10px);
  background-color: transparent;
  border-radius: 2px;
  border: 2px solid #5d575f;
}
input[type="checkbox"]:not(:checked) + span::after {
  content: "";
  position: absolute;
  width: 12px;
  height: 12px;
  transition: all 0.2s ease;
  opacity: 0;
  left: 4px;
  top: calc(50% - 6px);
  background-color: #7d25a9;
}
</style>
