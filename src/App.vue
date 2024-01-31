<template>
  <div class="container">
    <Sidebar
      :notes="notes"
      @openNotes="openNotes"
      @createNotes="createNotes"
      @createNotesInFolder="createNotesInFolder"
      @rename="rename"
      @renameFolder="renameFolder"
      @remove="remove"
      @removeFolder="removeFolder"
      @folderToggle="folderToggle"
    ></Sidebar>
    <EditorView
      :openedNote="openedNote"
      :editorStatus="isEditorOpen"
      @closeOpenedEditor="closeOpenedEditor"
      @saveOpenedNotes="saveOpenedNotes"
    ></EditorView>
  </div>
</template>
<script>
import Sidebar from "./components/Sidebar.vue";
import EditorView from "./components/EditorView.vue";

export default {
  components: {
    Sidebar,
    EditorView,
  },
  computed: {},
  data() {
    return {
      openedNote: null,
      notes: null,
      isEditorOpen: false,
    };
  },
  mounted() {
    this.getNotes();
  },
  methods: {
    getNotes() {
      this.notes = JSON.parse(localStorage.getItem("list"));
      if (!Array.isArray(this.notes)) {
        this.notes = [
          {
            id: 1,
            type: "file",
            name: "welcome",
            content: "welcome to my notes app.",
          },
        ];
      }
    },

    saveNotes() {
      localStorage.setItem("list", JSON.stringify(this.notes));
    },

    getLastId() {
      return (
        this.notes.reduce((maxId, note) => {
          return note.id > maxId ? note.id : maxId;
        }, 0) + 1
      );
    },

    getLastIdInFolder(folderId) {
      let lastFileId = 0;
      this.notes.forEach((note) => {
        if (note.type === "folder" && note.id === folderId) {
          note.files.forEach((file) => {
            lastFileId = Math.max(lastFileId, file.id) + 1;
          });
        }
      });
      return lastFileId;
    },

    openNotes(dataObj) {
      this.openedNote = this.notes.find((note) => {
        if (dataObj.folderId) {
          if (note.id === dataObj.folderId && note.type === "folder") {
            return note;
          }
        } else {
          if (note.id === dataObj.fileId && note.type === "file") {
            return note;
          }
        }
      });
      if (this.openedNote && this.openedNote.type === "folder") {
        this.openedNote = this.openedNote.files.find((file) => file.id === dataObj.fileId);
        this.openedNote = { ...this.openedNote, folderId: dataObj.folderId };
      }

      // set editor status
      this.isEditorOpen = true;
    },

    createNotes(dataObj) {
      dataObj.id = this.getLastId();
      this.notes.push(dataObj);
      this.saveNotes();
    },

    createNotesInFolder(dataObj, folderId) {
      dataObj.id = this.getLastIdInFolder(folderId);
      const folder = this.notes.find((note) => note.id === folderId && note.type === "folder");
      if (folder) {
        folder.files.push(dataObj);
        folder.isOpen = true;
      }
      this.saveNotes();
    },

    rename(dataObj) {
      if (dataObj.folderId) {
        const folder = this.notes.find(
          (folder) => folder.id === dataObj.folderId && folder.type === "folder"
        );
        folder.files.forEach((file) => {
          if (file.id === dataObj.fileId) {
            file.name = dataObj.newName;
          }
        });
      } else {
        const file = this.notes.find((note) => note.id === dataObj.fileId && note.type === "file");
        file.name = dataObj.newName;
      }
      this.saveNotes();
    },

    renameFolder(dataObj) {
      const folder = this.notes.find((folder) => folder.id === dataObj.folderId);
      folder.name = dataObj.newName;
      this.saveNotes();
    },

    remove(dataObj) {
      if (dataObj.folderId) {
        const folder = this.notes.find(
          (folder) => folder.id === dataObj.folderId && folder.type === "folder"
        );
        const newFiles = folder.files.filter((file) => file.id !== dataObj.fileId);
        folder.files = newFiles;
        if (folder.files.length === 0) {
          folder.isOpen = false;
        }
      } else {
        const newNotes = this.notes.filter((note) => note.id !== dataObj.fileId);
        this.notes = newNotes;
      }
      this.saveNotes();

      if (
        this.openedNote &&
        ((!dataObj.folderId && !this.openedNote.folderId) ||
          (dataObj.folderId === this.openedNote.folderId && dataObj.fileId === this.openedNote.id))
      ) {
        this.openedNote = null;
        this.isEditorOpen = false;
      }
    },

    removeFolder(folderId) {
      const newNotes = this.notes.filter((folder) => folder.id !== folderId);
      this.notes = newNotes;
      this.saveNotes();

      if (this.openedNote && folderId === this.openedNote.folderId) {
        this.openedNote = null;
        this.isEditorOpen = false;
      }
    },

    folderToggle(index) {
      this.notes[index].isOpen = !this.notes[index].isOpen;
      this.saveNotes();
    },

    saveOpenedNotes() {
      this.saveNotes();
    },

    closeOpenedEditor() {
      this.isEditorOpen = false;
      this.openedNote = null;
    },
  },
};
</script>

<style>
@import url("https://fonts.googleapis.com/css2?family=Ubuntu:ital,wght@0,400;0,700;1,400;1,700&display=swap");

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-family: "Ubuntu", "sans-serif", "system-ui";
}

#app {
  height: 100vh;
  width: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
}

.container {
  background-color: #045757;
  width: inherit;
  height: inherit;
  padding: 0.5rem;
  display: flex;
}

.sidebar {
  flex: 2;
}

.content {
  flex: 8;
}

@media screen and (max-width: 600px) {
  .container {
    flex-direction: column;
  }

  .sidebar {
    margin: 0;
  }
}
</style>
