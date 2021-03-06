<template>
    <v-layout row wrap>
        <v-flex xs12>
            <v-btn block @click="toggleRecording">
                <span v-if="isRecording" class="recordDot mr-2">&#x2B24;</span>
                {{ isRecording ? "Stop" : "Start" }} Recording
            </v-btn>
        </v-flex>
        <v-flex xs6>
            <v-btn block @click="load">Load Recording</v-btn>
        </v-flex>
        <v-flex xs6>
            <v-btn block @click="save">Save Recording</v-btn>
        </v-flex>
        <v-dialog v-model="errorDialog" max-width="500">
            <v-card>
                <v-card-title class="headline">Error</v-card-title>
                <v-card-text>{{ errorText }}</v-card-text>
                <v-card-actions>
                    <v-spacer></v-spacer>
                    <v-btn flat @click.native="errorDialog = false">OK</v-btn>
                </v-card-actions>
            </v-card>
        </v-dialog>
    </v-layout>
</template>

<script lang="ts">
import Vue from "vue";
import Component from "vue-class-component";
import { remote } from "electron";

@Component
export default class LoadSavePanel extends Vue {

    public errorDialog = false;
    public errorText = "";

    get isRecording() {
        return this.$store.state.database.isRecording;
    }
    
    public load() {
        remote.dialog.showOpenDialog({
            title: "Open recording",
            filters: [ { name: "sqlite", extensions: ["sqlite"] } ],
        }, (file) => {
            if (file && file[0]) {
                this.$store.state.database.loadFile(file[0]);
            }
        })
    }

    public save() {
        remote.dialog.showSaveDialog({
            title: "Save recording",
            filters: [ { name: "sqlite", extensions: ["sqlite"] } ],
        }, (file) => {
            if (file) {
                this.$store.state.database.saveFile(file);
            }
        })
    }

    public toggleRecording() {
        if (this.isRecording) {
            this.$store.state.database.stopRecording();
        } else {
            this.$store.state.database.startRecording();
        }
    }

}
</script>

<style>
@keyframes recordAnimation {
    from { opacity: 0; }
    to { opacity: 1; }
}
.recordDot {
    animation: recordAnimation 0.5s alternate 0s infinite;
}
</style>
