<template>
  <div class="sidebar">
    <div class="sidebar-header">
      <div class="app-name">
        <h1>Notes App</h1>
      </div>
      <div class="sidebar-action">
        <i @click="clickAddNote()" class="fa-solid fa-file-medical"></i>
        <i @click="clickAddFolder()" class="fa-solid fa-folder-plus"></i>
      </div>
    </div>
    <div class="sidebar-body">
      <div v-for="(element, index) in notes" :key="index">
        <div v-if="element.type === 'folder'" class="folder">
          <div class="folder-info">
            <div @click="clickHideFilesInFolder(index)" class="folder-title">
              <i
                :class="{ 'fa-angle-down': element.isOpen, 'fa-angle-right': !element.isOpen }"
                class="fa-solid"
              ></i>
              <i class="fa-solid fa-folder"></i>
              <p>{{ element.name }}</p>
            </div>
            <div class="folder-action">
              <i @click="clickAddFileInFolder(element.id)" class="fa-solid fa-file-medical"></i>
              <i @click="clickRenameFolder(element.id)" class="fa-solid fa-file-pen"></i>
              <i @click="clickRemoveFolder(element.id)" class="fa-solid fa-trash"></i>
            </div>
          </div>
          <div v-show="element.isOpen" class="note-list">
            <div v-for="(file, index) in element.files" :key="index" class="note note-in-folder">
              <div @click="clickedNote(file.id, element.id)" class="note-info">
                <i class="fa-solid fa-file"></i>
                <p class="note-title">{{ file.name }}</p>
              </div>
              <div class="note-action">
                <i @click="clickRename(file.id, element.id)" class="fa-solid fa-file-pen"></i>
                <i @click="clickRemove(file.id, element.id)" class="fa-solid fa-trash"></i>
              </div>
            </div>
          </div>
        </div>
        <div v-else class="note">
          <div @click="clickedNote(element.id, '')" class="note-info">
            <i class="fa-solid fa-file"></i>
            <p class="note-title">{{ element.name }}</p>
          </div>
          <div class="note-action">
            <i @click="clickRename(element.id, '')" class="fa-solid fa-file-pen"></i>
            <i @click="clickRemove(element.id, '')" class="fa-solid fa-trash"></i>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  props: {
    notes: {
      type: Array,
      default: [],
    },
  },
  data() {
    return {};
  },
  methods: {
    clickedNote(fileId, folderId) {
      this.$emit("openNotes", { fileId, folderId });
    },

    clickAddNote() {
      const name = prompt("Nama catatan :");
      if (!name) return;
      this.$emit("createNotes", {
        id: 0,
        type: "file",
        name: name,
        content: "",
      });
    },

    clickAddFolder() {
      const name = prompt("Nama folder :");
      if (!name) return;
      this.$emit("createNotes", {
        id: 0,
        type: "folder",
        name: name,
        isOpen: false,
        files: [],
      });
    },

    clickAddFileInFolder(folderId) {
      const name = prompt("Nama catatan :");
      if (!name) return;
      this.$emit(
        "createNotesInFolder",
        {
          id: 0,
          type: "file",
          name: name,
          content: "",
        },
        folderId
      );
    },

    clickRename(fileId, folderId) {
      const newName = prompt("Nama baru :");
      if (!newName) return;
      this.$emit("rename", { fileId, folderId, newName });
    },

    clickRenameFolder(folderId) {
      const newName = prompt("Nama baru :");
      if (!newName) return;
      this.$emit("renameFolder", { folderId, newName });
    },

    clickRemove(fileId, folderId) {
      const isRemove = confirm("Hapus catatan?");
      if (!isRemove) return;
      this.$emit("remove", { fileId, folderId });
    },

    clickRemoveFolder(folderId) {
      const isRemove = confirm("Hapus Folder beserta semua isinya?");
      if (!isRemove) return;
      this.$emit("removeFolder", folderId);
    },

    clickHideFilesInFolder(index) {
      this.$emit("folderToggle", index);
    },
  },
};
</script>

<style scoped>
.sidebar {
  background-color: #ffffff;
  height: 100%;
  padding: 0.5rem;
  border-radius: 0.6rem;
  margin-right: 0.5rem;
}

.sidebar-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  background-color: #045757;
  color: white;
  padding-inline: 0.6rem;
  padding-block: 0.4rem;
  border-radius: 0.4rem;
  margin-bottom: 1rem;
}

.app-name {
  font-size: 0.8rem;
  cursor: pointer;
}

.sidebar-action i {
  margin: 0.3rem;
  cursor: pointer;
}

.sidebar-body {
  border-top: 4px solid #000;
  padding-top: 0.4rem;
  /* background-color: #045757; */
}

.note {
  background-color: #e4e4e4;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding-block: 0.6rem;
  padding-inline: 0.5rem;
  border-radius: 0.4rem;
  margin-bottom: 0.4rem;
}

.note-info {
  flex: 1;
  display: flex;
  align-items: center;
  cursor: pointer;
  gap: 0.3rem;
}

.note-action i {
  padding-inline: 0.2rem;
  cursor: pointer;
}
.note-in-folder {
  margin: 0;
  margin-left: 1rem;
  margin-bottom: 0.4rem;
}

.folder {
  background-color: #045757;
  display: flex;
  flex-direction: column;
  justify-content: center;
  padding-block: 0.6rem;
  padding-inline: 0.5rem;
  border-radius: 0.4rem;
  margin-bottom: 0.4rem;
  gap: 0.6rem;
}

.folder-info {
  display: flex;
  /* margin-bottom: 0.6rem; */
}

.folder-title {
  flex: 1;
  color: #fff;
  display: flex;
  align-items: center;
  gap: 0.3rem;
  cursor: pointer;
}

.folder-action {
  display: flex;
  align-items: center;
  color: #fff;
  gap: 0.4rem;
  cursor: pointer;
}

@media screen and (max-width: 600px) {
  .sidebar {
    margin: 0;
    margin-bottom: 0.6rem;
  }
}
</style>
