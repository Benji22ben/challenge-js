<script setup lang="ts">
import Input from "./components/Input.vue";
import mapboxgl from "mapbox-gl";
import mapboxSdk from "@mapbox/mapbox-sdk"
</script>

<script lang="ts">
interface Member {
  firstname: String,
  lastname: String,
  address: String,
  phone: number,
  age: number,
  gender: String
}
interface Coordinates {
  latitude: String,
  longitude: String
}

export default {
  data() {
    return {
      firstname: "Jean",
      age: 30,
      year: 2022,
      buttonVisible: false,
      accessToken: "pk.eyJ1IjoiYmVuNDYwMCIsImEiOiJjbGU3N3NmdGowMndtM3ZsOGhsZWVoMWV0In0.LY8QIB0z866pzrTGCVxjCQ",
      family: [
        {
          firstname: "Jojo",
          lastname: "Bernard",
          age: "25",
          address: "123 Main St",
          phone: "555-1234",
          gender: "Male",
          coordinates: {
            longitude: 80.02,
            latitude: 30.03
          }
        },
        {
          firstname: "Marie",
          lastname: "Blachère",
          age: "29",
          address: "456 Oak Ave",
          phone: "555-5678",
          gender: "Female",
          coordinates: {
            longitude: 14.02,
            latitude: 15.03
          }
        },
        {
          firstname: "Jean",
          lastname: "Bernard",
          age: "20",
          address: "789 Elm St",
          phone: "555-9012",
          gender: "Male",
          coordinates: {
            longitude: 40.02,
            latitude: 30.03
          }
        },
        {
          firstname: "Paul",
          lastname: "Pogba",
          age: 17,
          address: "1011 Pine St",
          phone: "555-3456",
          gender: "Male",
          coordinates: {
            longitude: 5.900573938429103,
            latitude: 45.68649560138962
          }
        },
      ],
      newMember: {
        firstname: "",
        lastname: "",
        age: 0,
        address: "",
        phone: 0,
        gender: "",
        longitude: "",
        latitude: ""
      } as Member
    }
  },
  mounted() {
    this.addAge();
    mapboxgl.accessToken = 'pk.eyJ1IjoiYmVuNDYwMCIsImEiOiJjbGU3N3NmdGowMndtM3ZsOGhsZWVoMWV0In0.LY8QIB0z866pzrTGCVxjCQ';

    const mapboxClient = mapboxSdk({ accessToken: mapboxgl.accessToken });
    mapboxClient.geocoding
      .forwardGeocode({
        query: 'Wellington, New Zealand',
        autocomplete: false,
        limit: 1
      })
      .send()
      .then((response) => {
        if (
          !response ||
          !response.body ||
          !response.body.features ||
          !response.body.features.length
        ) {
          console.error('Invalid response:');
          console.error(response);
          return;
        }
        const feature = response.body.features[0];

        const map = new mapboxgl.Map({
          container: 'map',
          style: 'mapbox://styles/mapbox/streets-v12', // style URL
          center: [5.898514002087383, 45.68925369065059], // starting position [lng, lat]
          zoom: 4
        });

        this.markerForEachUser(this.family, map)

        // Create a marker and add it to the map.
        new mapboxgl.Marker().setLngLat(feature.center).addTo(map);
      });
  },
  methods: {
    addAge() {
      setInterval(() => {
        this.age += 1
        this.year += 1
      }, 3000);
    },
    changeName() {
      this.firstname = this.firstname === "Patrick" ? "Jean" : "Patrick"
    },
    familySorted() {
      return this.family.sort((a: { age: number; }, b: { age: number; }) => a.age - b.age)
    },
    addFamilyMember() {
      this.family.push({
        firstname: "Jojo",
        lastname: "Bernard",
        age: "25",
        address: "fefzeiu",
        phone: "crefkn"
      }
      )
    },
    deleteFamilyMember(firstname: any) {
      this.family.splice(this.family.firstname = firstname, 1);
    },
    submitForm() {
      if (this.newMember.age < 10) {
        alert("Le membre de la famille doit avoir + de 10 ans !")
        return
      }

      const member = {
        firstname: this.newMember.firstname,
        lastname: this.newMember.lastname,
        age: this.newMember.age,
        address: this.newMember.address,
        phone: this.newMember.phone,
        gender: this.newMember.gender
      }

      this.family.push(member)
    },
    markerForEachUser(users, map) {
      for (let i = 0; users.length; i++) {
        const marker = new mapboxgl.Marker()
          .setLngLat([users[i].coordinates.longitude, users[i].coordinates.latitude])
          .addTo(map);
      }
    },
  }
}
</script>

<template>
  <main class="w-full h-full">
    <div id="presentation">
      <p class="text-white">
        Bonjour je m'appelle {{ firstname }}
      </p>
    </div>
    <div id="age">
      <p class="text-white">
        "En {{ year }}, {{ firstname }} aura {{ age }} ans"
      </p>
    </div>
    <div id="family">
      <div v-for="(member, index) in familySorted()" :key="familySorted().firstname">
        <p>
          Prénom : {{ member.firstname }}
          Nom : {{ member.lastname }}
          Age : {{ member.age }}
          Adresse : {{ member.address ? member.address : "Non renseigné" }}
          Téléphone : {{ member.phone ? member.phone : "Non renseigné" }}
          Genre : {{ member.gender ? member.gender : "Non renseigné" }}
        </p>
        <button @click="deleteFamilyMember(member.firstname)" class="text-[#1a1a1a] p-2 rounded bg-white">
          Delete {{ member.firstname }}
        </button>
      </div>
    </div>
    <button v-if="buttonVisible" class="mt-2 text-[#1a1a1a] p-4 rounded bg-white" @click="changeName()" id="crazy-button">
      Change name
    </button>
    <button class="mt-2 text-[#1a1a1a] p-4 rounded bg-white" @click="addFamilyMember()" id="add-family-member">Add Family
      Member</button>
    <h2 class="mt-10">Add a new family Member</h2>
    <Input v-model="newMember.firstname" name="Prénom" id="prenom" type="text" required />
    <Input v-model="newMember.lastname" name="Nom" id="nom" type="text" required />
    <mapbox-address-autofill
      access-token="pk.eyJ1IjoiYmVuNDYwMCIsImEiOiJjbGU3N3NmdGowMndtM3ZsOGhsZWVoMWV0In0.LY8QIB0z866pzrTGCVxjCQ">
      <Input v-model="newMember.address" autocomplete="shipping street-address" name="Adresse" id="address" type="text"
        required />
    </mapbox-address-autofill>
    <Input v-model="newMember.phone" name="Téléphone" id="phone" type="tel" required />
    <Input v-model="newMember.age" name="Age" id="age" type="number" pattern="[0-9]{10}" required />
    <Input v-model="newMember.gender" name="Genre" id="gender" type="text" required />
    <button class="bg-white text-black mt-10" type="submit" @click="submitForm()">Submit</button>
    <div class="mt-10 h-64" id="map"></div>
</main>
</template>