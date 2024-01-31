<template>
  <div class="content">
    <div v-if="editorStatus" class="container">
      <div class="title">
        <div class="title-info">
          <i class="fa-solid fa-file"></i>
          <p class="title-name">{{ editedNote.name }}</p>
          <i @click="clickCloseEditor()" class="fa-solid fa-square-xmark close"></i>
        </div>
        <div class="title-action">
          <button @click="clickSaveNotes()" class="save-button">Save</button>
        </div>
      </div>
      <div class="editor-view">
        <textarea v-model="editedNote.content" name="editor" id="editor" class="editor"></textarea>
      </div>
    </div>
    <div v-else class="no-content">
      <img class="no-content-image" src="../assets/favicon.svg" alt="logo icon" />
      <p class="no-content-information">No notes have been opened yet.</p>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    openedNote: {
      type: Object,
      default: null,
    },
    editorStatus: {
      type: Boolean,
      default: false,
    },
  },
  computed: {
    editedNote() {
      return this.openedNote ? JSON.parse(JSON.stringify(this.openedNote)) : null;
    },
  },
  methods: {
    clickSaveNotes() {
      this.openedNote.content = this.editedNote.content;
      this.$emit("saveOpenedNotes");
    },

    clickCloseEditor() {
      if (this.editedNote.content !== this.openedNote.content && !confirm("Close without save?")) {
        return;
      }
      this.$emit("closeOpenedEditor", this.editedNote);
    },
  },
};
</script>

<style scoped>
.content {
  background-color: #ffffff;
  padding: 0.5rem;
  border-radius: 0.6rem;
  display: flex;
  justify-content: center;
  align-items: center;
}

.container {
  flex: 1;
  padding: 0;
  border-radius: 0.6rem;
  height: 100%;
  display: flex;
  flex-direction: column;
}

.title {
  background-color: #045757;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0.6rem;
  border-top-right-radius: 0.4rem;
  border-top-left-radius: 0.4rem;
}

.title-info {
  color: white;
  display: flex;
  align-items: center;
  gap: 0.3rem;
}

.close {
  margin-left: 1rem;
  cursor: pointer;
}

.save-button {
  padding-inline: 1rem;
  padding-block: 0.3rem;
  background-color: #e4e4e4;
  border: none;
  border-radius: 0.25rem;
  font-weight: 700;
  cursor: pointer;
  transition: all 0.3s ease-in;
}

.save-button:hover {
  opacity: 0.9;
}

.save-button:active {
  opacity: 0.6;
}

.editor-view {
  flex: 1;
}

.editor {
  background-color: #e4e4e4;
  width: 100%;
  height: 100%;
  padding: 1.5rem;
  outline: none;
  border: none;
  resize: none;
  border-bottom-left-radius: 0.4rem;
  border-bottom-right-radius: 0.4rem;
  font-size: 1.2rem;
}

.no-content {
  text-align: center;
}

.no-content-image {
  width: 13rem;
  margin-bottom: 1rem;
}

.no-content-information {
  font-size: 1.5rem;
  font-weight: bold;
}

@media screen and (max-width: 600px) {
  .no-content-image {
    width: 8rem;
  }
  .no-content-information {
    font-size: 0.8rem;
  }
}
</style>
