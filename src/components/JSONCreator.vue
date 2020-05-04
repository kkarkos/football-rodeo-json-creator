<template>
  <div>
    <h1 class="title">File trimmer</h1>
    <button
      type="button"
      class="button"
      @click="handleOpenFiles(`trimmed`)"
    >Select files to be auto trimmed</button>
    <hr />
    <h1 class="title">Football Rodeo JSON Creator</h1>
    <form v-on:submit.prevent="onSubmit">
      <b-field label="Team Name">
        <b-input required v-model="teamName"></b-input>
      </b-field>
      <b-field label="Country">
        <b-input required v-model="country"></b-input>
      </b-field>
      <b-field label="Icon (For int use 'earth_africa', for german teams 'germany'">
        <b-input required v-model="icon"></b-input>
      </b-field>
      <b-field label="Division (1stTier, 2ndTier, Legends etc)">
        <b-input required v-model="division"></b-input>
      </b-field>
      <button type="button" class="button" @click="handleOpenFiles">Select files</button>
      <hr />
      <div v-for="item in fileNames" :key="item.id">
        <Player :data="item" />
        <hr />
      </div>
      <button type="submit" class="button" v-if="fileNames.length">Save JSON</button>
    </form>
  </div>
</template>

<script>
const { dialog } = require("electron").remote;
const fs = require("fs");
const path = require("path");
import Player from "./Player";

export default {
  name: "JSONCreator",
  components: {
    Player
  },
  data() {
    return {
      division: "",
      teamName: "",
      country: "",
      icon: "",
      fileNames: []
    };
  },
  methods: {
    handleOpenFiles(action) {
      dialog
        .showOpenDialog({
          properties: ["openFile", "multiSelections"]
        })
        .then(result => {
          if (action === "trimmed") {
            // trim file names and re-save at same location
            result.filePaths.map(f => {
              const file = path.parse(f);
              const name = file.name.replace(/[^A-Z0-9]/gi, "-").toLowerCase();
              const renamed = `${file.dir}/${name}${file.ext}`;

              fs.rename(f, renamed, err => {
                if (err) {
                  alert("An error ocurred updating the file" + err.message);
                  console.log(err);
                  return;
                }
                console.log("File has been renamed and saved", renamed);
              });
            });
          } else {
            this.fileNames = [
              ...this.fileNames,
              ...result.filePaths.map(f => {
                return { id: this.$uuid.v1(), path: f };
              })
            ];
          }
        })
        .catch(err => {
          console.log(err);
        });
    },
    onSubmit(event) {
      const players = this.fileNames.map(p => {
        return {
          id: p.id,
          firstName: event.target[`${p.id}-firstName`].value,
          lastName: event.target[`${p.id}-lastName`].value,
          imageName: path.parse(p.path).name,
          imageSourceName: event.target[`${p.id}-imageSourceName`].value
        };
      });

      const team = {
        id: this.teamName.replace(/[^A-Z0-9]/gi, "-").toLowerCase(),
        name: this.teamName,
        country: this.country.toLowerCase(),
        icon: this.icon.toLowerCase(),
        division: this.division.trim()
      };

      team.players = players;

      dialog.showSaveDialog(fileName => {
        if (fileName === undefined) {
          console.log("You didn't save the file");
          return;
        }

        if (team.id === "") {
          console.log("File name missing!");
          return;
        }

        const data = JSON.stringify(team);

        // ignore selected file name and replace with fixed team name
        const find = fileName.split("/");
        const word = find.slice(-1)[0].toString();
        const path = fileName.replace(word, "");

        fs.writeFileSync(`${path}${team.id}.json`, data, err => {
          if (err) {
            alert("An error ocurred creating the file " + err.message);
          }
          alert("The file has been succesfully saved");
        });
      });
    }
  }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped></style>
