<template>
<section class="hero is-info">
  <div class="hero-body">
    <div class="container">
      <div class="columns is-centered">
        <div class="column is-5-tablet is-4-desktop is-4-widescreen">
          <form class="box">
            <div class="field has-text-centered">
              <span class="icon" style="width: 3rem; height: 3rem;">
									<i class="fa fa-cog fas fa-3x"></i>
								</span><br> Site Settings
            </div>
            <div class="field">
              <label class="label">Square Logo</label>
              <div class="control has-icons-left">
                <div class="file has-name is-boxed">
                  <label class="file-label">
                      <input class="file-input" type="file" name="logo"
                            v-on:change="uploadImage('squareLogo', $event)">
                      <span class="file-cta">
												<span class="file-icon">
													<i class="fas fa-upload"></i>
												</span>
												<span class="file-label">
													Choose a file…
												</span>
											</span>
                      <!--<span class="file-name">
												{{sqLogo}}
											</span>-->
                    </label>
                </div>
              </div>
            </div>
            <br>
            <div class="field">
              <label class="label">Horizontal Logo</label>
              <div class="control has-icons-left">
                <div class="file has-name is-boxed">
                  <label class="file-label">
                      <input class="file-input" type="file" name="logo"
                            v-on:change="uploadImage('horizontalLogo', $event)">
                      <span class="file-cta">
												<span class="file-icon">
													<i class="fas fa-upload"></i>
												</span>
												<span class="file-label">
													Choose a file…
												</span>
											</span>
                      <!--<span class="file-name">
												{{rectLogo}}
											</span>-->
                    </label>
                </div>
              </div>
            </div>
            <div class="field">
              <label class="label">Tagline</label>
              <div class="control has-icons-left">
                <input class="input" type="text" v-model="tagline" id="tagline" placeholder="e.g. Serverless Blogging Engine" autocomplete="name" required>
              </div>
            </div>
            <div class="field">
              <label class="label">Blog Menu</label>
              <div class="control has-icons-left">
                <div class="control">
                  <label class="checkbox">
                      <input type="checkbox" v-model="disableBlog">
                      Disable Blog Menu
                    </label>
                </div>
              </div>
            </div>
            <div class="field">
              <label class="label">New Registrations?</label>
              <div class="control has-icons-left">
                <div class="control">
                  <label class="checkbox">
                      <input type="checkbox" v-model="disableSignups">
                      Disable New Registrations
                    </label>
                </div>
              </div>
            </div>
            <div class="field is-grouped" style="margin-top: 1.5rem;">
              <div class="control">
                <button class="button is-info" @click="saveSettings">Save Settings</button>
              </div>
              <div class="control">
                <a class="button is-text" style="text-decoration: none;" href="home">Cancel</a>
              </div>
            </div>
            <!-- <div class="field">
                    <button class="button is-success">
                      Login
                    </button>
                </div> -->
            <!-- function uploadImage(file) { // Upload file and get url from firebase server. Make an API call, upload the file and get the URL which can be embedded into the editor. const user = this.$cookie.getJSON("user"); const moment = require("moment"); const now
            = moment().format("YYYY-MM-DD HH:mm:ss.ms Z"); const storagePath = `${user.id}/images/${now}_${file.name}`; firebase .storage() .ref(storagePath) .put(file) .then(snapshot => { console.log('image upload success'); return snapshot.ref.getDownloadURL();
            }) .then(downloadURL => { console.log('writing download url to db'); //const url = "https://avatars2.githubusercontent.com/u/16257851?s=88&v=4"; const url = downloadURL; const range = this.$refs.editor.quill.getSelection(); this.$refs.editor.quill.insertEmbed(range.index,
            "image", url); let db = firebase.firestore(); const settings = { timestampsInSnapshots: true }; db.settings(settings); db.collection('images').add({ name: file.name, path: storagePath, publicUrl: url, createdBy: user.id, createdAt: now, })
            .then(function(docRef) { console.log("Document written with ID: ", docRef.id); }) .catch(function(error) { console.error("Error adding document: ", error); }); }) .catch(error => { console.error(error); this._alert(true, 'error', error.message);
            }); } -->
          </form>
        </div>
      </div>
    </div>
  </div>
</section>
</template>
<script type="text/javascript">
import firebase from "firebase";
import { globalVariables } from "./../main";

function firebaseUpload(file, logoType, user, onSuccessCallback) {
  const storagePath = `${user.id}/images/${logoType}.jpg`;
  firebase
    .storage()
    .ref(storagePath)
    .put(file)
    .then(snapshot => {
      console.log("image upload success");
      return snapshot.ref.getDownloadURL();
    })
    .then(downloadURL => {
      // you can assign keys for square logo and horizontal logo with downloadURL value here
      // or just provide a callback
      onSuccessCallback(downloadURL);
      // when 'save settings'  is pressed line 314 uploads the key and value (imageurl)
    })
    .catch(error => {
      console.error(error);
      alert(error.message);
    });
}
export default {
  data() {
    return {
      tagline: "", // 'Serverless Blogging Engine',
      disableBlog: false,
      disableSignups: false,
      settingName: ["tagline", "disableBlog", "disableSignups"],
      settings: []
    };
  },
  computed: {
    inputType: function() {
      const { deploymentTarget, LOCALHOST, FBASE } = globalVariables;
      return deploymentTarget === FBASE ? "file" : "file";
    }
  },
  created: function() {
    // fetch settings and set values
    const vm = this;
    const { deploymentTarget, LOCALHOST, FBASE } = globalVariables;
    console.log("deployment target is " + deploymentTarget);
    this.$root.$upload.new("squareLogo", {
      url: "v1/settings/logo",
      name: "logo",
      body: {
        logoType: "squareLogo"
      },
      onSuccess: res => {
        console.log("Logo uploaded Successfully");
        location.reload();
      },
      onError(error) {
        alert("Unable to upload squareLogo pic");
      }
    });
    this.$root.$upload.new("horizontalLogo", {
      url: "v1/settings/logo",
      name: "logo",
      body: {
        logoType: "horizontalLogo"
      },
      onSuccess: res => {
        console.log("Logo uploaded Successfully");
        location.reload();
      },
      onError(error) {
        alert("Unable to upload horizontalLogo pic");
      }
    });
    switch (deploymentTarget) {
      case LOCALHOST:
        axios
          .get("/v1/settings", {})
          .then(function(response) {
            console.log(response);
            let settings = response.data.settings;
            vm.settings = settings;
            for (let i in settings) {
              let setting = settings[i];
              switch (setting.name) {
                case "disableBlog":
                case "disableSignups":
                  vm[setting.name] = setting.value === "1";
                  break;
                default:
                  vm[setting.name] = setting.value;
              }
            }
          })
          .catch(function(error) {
            console.log(error);
            // if error is 401 unauthorize, logout the user.
            if (error.toString().indexOf("401") !== -1) {
              console.log("Logging you out...");
              vm.logout();
            }
          });
        break;
      case FBASE:
        let db = firebase.firestore();
        const dbSettings = {
          timestampsInSnapshots: true
        };
        db.settings(dbSettings);
        const user = this.$cookie.getJSON("user");
        db
          .collection("settings")
          .where("createdBy", "==", user.id)
          .get()
          .then(function(querySnapshot) {
            let settings = [];
            querySnapshot.forEach(function(doc) {
              settings.push(doc.data());
            });
            for (let i in settings) {
              let setting = settings[i];
              switch (setting.name) {
                case "disableBlog":
                case "disableSignups":
                  vm[setting.name] = setting.value === "1";
                  break;
                default:
                  vm[setting.name] = setting.value;
              }
            }
          })
          .catch(function(error) {
            console.log("Error getting settings: ", error);
          });
        break;
    }
  },
  methods: {
    uploadImage: function(logoType, event) {
      const { deploymentTarget, LOCALHOST, FBASE } = globalVariables;
      const file = event.target.files[0];
      if (deploymentTarget === FBASE) {
        const user = this.$cookie.getJSON("user");
        firebaseUpload(file, logoType, user, resp => {
          console.log("image uploaded with url", resp);
        });
      } else {
        const formData = new FormData();
        formData.append("logo", file);
        formData.append("logoType", logoType);
        axios
          .post("v1/settings/logo", formData)
          .then(resp => {
            console.log("Logo uploaded Successfully");
            location.reload();
          })
          .catch(err => console.log("Logo uploading error", err));
      }
    },
    // updateImage: function(logoType) {
    //   this.$root.$upload.select(logoType);
    // },
    saveSettings: function() {
      const vm = this;
      let settings = vm.settings;
      const { deploymentTarget, LOCALHOST, FBASE } = globalVariables;
      console.log("deployment target is " + deploymentTarget);
      // updating existing settings
      for (let i in settings) {
        let setting = settings[i];
        // switch(setting.name){
        // case "tagline":
        let settingData = {
          id: setting.id,
          name: setting.name,
          // value: vm.tagline
          value: vm[setting.name]
        };
        if (setting.name) {
          switch (deploymentTarget) {
            case LOCALHOST:
              axios
                .put("/v1/settings/" + setting.id, settingData)
                .then(function(response) {
                  console.log(response);
                })
                .catch(function(error) {
                  console.log(error);
                });
              break;
            case FBASE:
              let db = firebase.firestore();
              const dbSettings = {
                timestampsInSnapshots: true
              };
              const moment = require("moment");
              settingData.updatedAt = moment().format(
                "YYYY-MM-DD HH:mm:ss.ms Z"
              );
              db.settings(dbSettings);
              db
                .collection("settings")
                .doc(setting.id)
                .update(settingData)
                .then(function(docRef) {
                  console.log("settings saved!");
                })
                .catch(function(error) {
                  console.error("Error adding document: ", error);
                });
              break;
          }
        }
        // break;
        // }
      }
      for (let settingKey of vm.settingName) {
        const found = vm.settings.find(f => settingKey === f.name);
        if (!found && vm[settingKey]) {
          let settingData = {
            name: settingKey,
            value: vm[settingKey]
          };
          switch (deploymentTarget) {
            case LOCALHOST:
              axios
                .post("/v1/settings/", settingData)
                .then(function(response) {
                  console.log(response);
                })
                .catch(function(error) {
                  console.log(error);
                });
              break;
            case FBASE:
              let db = firebase.firestore();
              const dbSettings = {
                timestampsInSnapshots: true
              };
              db.settings(dbSettings);
              const uuidv4 = require("uuid/v4");
              settingData.id = uuidv4();
              settingData.createdBy = vm.$cookie.getJSON("user").id;
              const moment = require("moment");
              settingData.createdAt = moment().format(
                "YYYY-MM-DD HH:mm:ss.ms Z"
              );
              settingData.updatedAt = moment().format(
                "YYYY-MM-DD HH:mm:ss.ms Z"
              );
              db
                .collection("settings")
                .doc(settingData.id)
                .set(settingData)
                .then(function(docRef) {
                  console.log("settings added!");
                })
                .catch(function(error) {
                  console.error("Error adding document: ", error);
                });
              break;
          }
        }
      }
      // creating a setting if it didn't exist before
      /*
              var notThere = true;
              if(vm.tagline.length){
                  // if the tagline isn't there in vm.settings, create it.
                  for(let i in settings) {
                      let setting = settings[i];
                      if(setting.name == 'tagline') notThere = false;
                  }
                  if(notThere) {
                      // create the new setting if it's not there
                      let settingData = {
                            name: 'tagline',
                              value: vm.tagline
                        };
                      axios
                            .post("/v1/settings", settingData)
                            .then(function(response) {
                                console.log(response);
                            })
                            .catch(function(error) {
                                console.log(error);
                          });
                  }
              }
              */
    }
  }
};
</script>