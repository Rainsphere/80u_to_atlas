<template>
  <div id="app">
    <div class="export-instructions">
      <p>
        Export your set from
        <a href="https://sixtyupgrades.com/">sixtyupgrades.com</a> and
        paste it into the textarea below.
      </p>
    </div>
    <textarea
      ref="sixtyUpgrades"
      placeholder="Paste 60 Upgrades Export Here"
      id="sixty-upgrades-export"
    ></textarea>
    <button @click="performConversion">Convert</button>
    <textarea
      ref="atlasImport"
      id="atlas-import"
    ></textarea>
    <button
      @click="copyToClipboard"
      :class="{ has_copied: hasCopied }"
    >
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
  setup () {
    const hasCopied = ref(false);
    const sixtyUpgrades = ref("");
    const atlasImport = ref("");

    const performConversion = () => {
      //confirm that the user has pasted something and that it is a json object
      if (sixtyUpgrades.value.value.length > 0) {
        try {
          hasCopied.value = false;
          const parsedsixtyUpgrades = JSON.parse(
            sixtyUpgrades.value.value
          );
          atlasImport.value.value =
            convertsixtyUpgradesToAtlas(parsedsixtyUpgrades);
        } catch (e) {
          console.error("Invalid JSON");
        }
      }
    };

    const convertsixtyUpgradesToAtlas = (parsedsixtyUpgrades) => {
      console.log(parsedsixtyUpgrades);
      //confirm that parsedsixtyUpgrades is an object and contains "items"
      if (
        typeof parsedsixtyUpgrades === "object" &&
        Object.prototype.hasOwnProperty.call(
          parsedsixtyUpgrades,
          "items"
        )
      ) {
        //go through each item and generate a comma delimited string using the item id
        const atlasImportString = parsedsixtyUpgrades.items
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
      sixtyUpgrades,
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
  background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
  padding: 0;
  margin: 0;
  min-height: 100vh;
}

* {
  box-sizing: border-box;
}

#app {
  font-family: "Segoe UI", Tahoma, Geneva, Verdana, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #e8e8e8;
  padding: 20px;
  max-width: 1000px;
  margin: 0 auto;
}

.export-instructions,
#atlas-import-instructions {
  background: rgba(255, 255, 255, 0.05);
  border: 1px solid rgba(255, 255, 255, 0.1);
  border-radius: 12px;
  padding: 20px 30px;
  margin: 30px 0;
  backdrop-filter: blur(10px);
}

.export-instructions {
  margin-top: 40px;
}

p {
  color: rgba(255, 255, 255, 0.9);
  font-size: 16px;
  line-height: 1.6;
  margin: 0;
}

a {
  color: #4a9eff;
  text-decoration: none;
  font-weight: 600;
  transition: color 0.3s ease;
}

a:hover {
  color: #6bb3ff;
  text-decoration: underline;
}

#sixty-upgrades-export,
#atlas-import {
  width: 100%;
  height: 250px;
  background: rgba(20, 20, 30, 0.8);
  color: #fff;
  border: 2px solid rgba(74, 158, 255, 0.3);
  border-radius: 12px;
  padding: 16px;
  font-family: "Consolas", "Monaco", monospace;
  font-size: 14px;
  resize: vertical;
  transition: all 0.3s ease;
  margin: 20px 0;
}

#sixty-upgrades-export:focus,
#atlas-import:focus {
  outline: none;
  border-color: #4a9eff;
  box-shadow: 0 0 0 3px rgba(74, 158, 255, 0.1);
  background: rgba(20, 20, 30, 0.95);
}

#sixty-upgrades-export::placeholder {
  color: rgba(255, 255, 255, 0.4);
}

button {
  margin: 15px 10px;
  background: linear-gradient(135deg, #4a9eff 0%, #357abd 100%);
  color: #fff;
  border: none;
  border-radius: 8px;
  width: 180px;
  height: 45px;
  font-size: 15px;
  font-weight: 600;
  cursor: pointer;
  transition: all 0.3s ease;
  box-shadow: 0 4px 15px rgba(74, 158, 255, 0.3);
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

button:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(74, 158, 255, 0.4);
  background: linear-gradient(135deg, #5aaeff 0%, #4589cd 100%);
}

button:active {
  transform: translateY(0);
  box-shadow: 0 2px 10px rgba(74, 158, 255, 0.3);
}

button.has_copied {
  background: linear-gradient(135deg, #10b981 0%, #059669 100%);
  box-shadow: 0 4px 15px rgba(16, 185, 129, 0.3);
}

button.has_copied:hover {
  background: linear-gradient(135deg, #34d399 0%, #10b981 100%);
  box-shadow: 0 6px 20px rgba(16, 185, 129, 0.4);
}
</style>
