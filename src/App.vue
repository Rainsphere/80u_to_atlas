<template>
    <div id="app">
        <div class="export-instructions">
            <p>
                Export your set from
                <a href="https://eightyupgrades.com/">eightyupgrades.com</a> and
                paste it into the textarea below.
            </p>
        </div>
        <textarea
            ref="eightyUpgrades"
            placeholder="Paste 80 Upgrades Export Here"
            id="eighty-upgrades-export"
        ></textarea>
        <button @click="performConversion">Convert</button>
        <textarea ref="atlasImport" id="atlas-import"></textarea>
        <button @click="copyToClipboard" :class="{ has_copied: hasCopied }">
            <span v-if="hasCopied">Copied!</span>
            <span v-else>Copy to Clipboard</span>
        </button>
        <div id="atlas-import-instructions">
            <p>
                Copy the above text and paste it via the "Import Item List"
                option in the Atlas addon.
            </p>
        </div>
    </div>
</template>

<script>
import { ref } from "vue";
export default {
    name: "App",
    setup() {
        const hasCopied = ref(false);
        const eightyUpgrades = ref("");
        const atlasImport = ref("");

        const performConversion = () => {
            //confirm that the user has pasted something and that it is a json object
            if (eightyUpgrades.value.value.length > 0) {
                try {
                    hasCopied.value = false;
                    const parsedEightyUpgrades = JSON.parse(
                        eightyUpgrades.value.value
                    );
                    atlasImport.value.value =
                        convertEightyUpgradesToAtlas(parsedEightyUpgrades);
                } catch (e) {
                    console.error("Invalid JSON");
                }
            }
        };

        const convertEightyUpgradesToAtlas = (parsedEightyUpgrades) => {
            console.log(parsedEightyUpgrades);
            //confirm that parsedEightyUpgrades is an object and contains "items"
            if (
                typeof parsedEightyUpgrades === "object" &&
                Object.prototype.hasOwnProperty.call(
                    parsedEightyUpgrades,
                    "items"
                )
            ) {
                //go through each item and generate a comma delimited string using the item id
                const atlasImportString = parsedEightyUpgrades.items
                    .map((item) => `i:${item.id}`)
                    .join(",");
                return atlasImportString;
            }
        };

        const copyToClipboard = () => {
            // atlasImport.value.select();
            // atlasImport.value.setSelectionRange(0, 99999);
            navigator.clipboard.writeText(atlasImport.value.value);
            hasCopied.value = true;
        };
        return {
            eightyUpgrades,
            atlasImport,
            performConversion,
            copyToClipboard,
            hasCopied,
        };
    },
};
</script>

<style>
body {
    background: #18181b;
    padding: 0px;
    margin: 0px;
}
* {
    box-sizing: border-box;
}
#app {
    font-family: Avenir, Helvetica, Arial, sans-serif;
    -webkit-font-smoothing: antialiased;
    -moz-osx-font-smoothing: grayscale;

    color: #2c3e50;
    padding: 0px 20px;
    max-width: 900px;
    margin: 60px auto 0px auto;
}
button {
    margin: 10px;
    background: linear-gradient(#22a5fd, #2097ff);
    color: #fff;
    border: 1px solid transparent;
    border-top-color: #55bafd;
    border-bottom-color: #078bff;
    outline: none;
    border-radius: 5px;
    width: 170px;
    height: 30px;
}
button.has_copied {
    color: #e0e0e0;
    background: none;
    border-color: #a2a2a2;
}
p {
    color: rgba(255, 255, 255, 0.8);
}
#eighty-upgrades-export,
#atlas-import {
    width: 100%;
    height: 200px;
    background: #222227;
    color: #fff;
    border: 1px solid rgba(255, 255, 255, 0.8);
}
a {
    color: #2097ff;
}
</style>
