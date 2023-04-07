<template>
  <div class="bg-[#0F172A] p-8 h-full w-full">
    <div class="flex gap-8 p-2 bg-[#1E293B] text-center rounded-xl my-2">
      <div>
        <label class="pr-4 text-white"> Fruite name: </label>
        <input type="text" placeholder="Enter the fruit name" v-model="filterOption.name" class="outline-none bg-transparent text-white border-[1px] border-gray-500 rounded p-1" />
      </div>
      <div>
        <label class="pr-4 text-white"> Fruite family: </label>
        <input type="text" placeholder="Enter the fruit family" v-model="filterOption.family" class="outline-none bg-transparent text-white border-[1px] border-gray-500 rounded p-1" />
      </div>
      <div>
        <label class="pr-4 text-white"> Favorite: </label>
        <input type="checkbox" v-model="filterByFavo">
      </div>
    </div>
    <table class="table-fixed bg-[#1E293B] text-center rounded-xl w-full">
      <thead class="text-white border-b-2 border-[#0F172A]">
        <tr>
          <th class="w-1/12 p-4">id</th>
          <th class="w-1/12">Genus</th>
          <th class="w-1/12">Name</th>
          <th class="w-1/12">Family</th>
          <th class="w-1/12">Order</th>
          <th class="w-1/2">Nutritions</th>
          <th class="w-1/12">Favorite</th>
        </tr>
      </thead>
      <tbody v-for="(fruit, index) in filteredFruits" :key = "index">
        <tr class="text-[#8492A7]">
          <td class="p-2 text-[#975280]">{{ fruit.id }}</td>
          <td>{{ fruit.genus }}</td>
          <td>{{ fruit.name }}</td>
          <td>{{ fruit.family }}</td>
          <td>{{ fruit.order_name }}</td>
          <td>
            <div class="flex justify-between px-2">
              <div>
                Calories: {{ fruit.nutritions.calories }}
              </div>
                Carbohydrates: {{ fruit.nutritions.carbohydrates }}
              <div>
                Fat: {{ fruit.nutritions.fat }}
              </div>
              <div>
                Protein: {{ fruit.nutritions.protein }}
              </div>
              <div>
                Sugar: {{ fruit.nutritions.sugar }}
              </div>
            </div>
          </td>
          <td>
            <input type="checkbox" :value="fruit.name" v-model="favoFruits" />
          </td>
        </tr>
      </tbody>
    </table>
  </div>
  
</template>

<script lang="ts">
import Vue from 'vue'
// const dummyData: any = [
//   {
//     "genus": "Malus",
//     "name": "Apple",
//     "id": 6,
//     "family": "Rosaceae",
//     "order": "Rosales",
//     "nutritions": {
//       "carbohydrates": 11.4,
//       "protein": 0.3,
//       "fat": 0.4,
//       "calories": 52,
//       "sugar": 10.3
//     }
//   }, {
//     "genus": "Prunus",
//     "name": "Apricot",
//     "id": 35,
//     "family": "Rosaceae",
//     "order": "Rosales",
//     "nutritions": {
//       "carbohydrates": 3.9,
//       "protein": 0.5,
//       "fat": 0.1,
//       "calories": 15,
//       "sugar": 3.2
//     }
//   },
// ];

class Nutrition{
  carbohydrates:number;
  protein:number;
  fat:number;
  calories: number;
  sugar: number;
    constructor(carbohydrates:number, protein:number, fat:number, calories:number, sugar:number){
    this.carbohydrates = carbohydrates;
    this.protein = protein;
    this.fat = fat;
    this.calories = calories;
    this.sugar = sugar;
    
  }
}

class Fruit {
  name: string;
  genus: string;
  id:number;
  family:string;
  order_name:string;
  nutritions:Nutrition;

  constructor(name: string, genus: string, id:number, family:string, order_name:string, nutritions:Nutrition) {
    this.name = name;
    this.genus = genus;
    this.id = id;
    this.family = family;
    this.order_name = order_name;
    this.nutritions = nutritions;
  }
}

export default Vue.extend({
  name: 'IndexPage',
  data(){
    return{
      fruits:[
       new Fruit("", "", 0, "", "", new Nutrition(0, 0, 0, 0, 0))
      ],
      filterOption: {
        name: "",
        family: ""
      },
      favoFruits: [""],
      filterByFavo: false,
    }
  },
  computed: {
    filteredFruits() {
      return this.fruits.filter((fruit) => {
        return fruit.name.includes(this.filterOption.name) && fruit.family.includes(this.filterOption.family) && (this.filterByFavo && this.favoFruits.indexOf(fruit.name) !== -1 || this.filterByFavo === false)
      })
    }
  },
  async fetch() {
    await fetch('http://localhost:8000/fruit').then((response: any) => {
      return response.json();
    })
    .then(({data}) => {
      const fruitData : Fruit[] = [];
      data.map((one:any)=> {
          fruitData.push({
            id: one.id,
            genus: one.genus,
            name: one.name,
            family: one.family,
            order_name: one.order_name,
            nutritions: {
                carbohydrates: JSON.parse(one.nutritions).carbohydrates,
                protein: JSON.parse(one.nutritions).protein,
                fat: JSON.parse(one.nutritions).fat,
                calories: JSON.parse(one.nutritions).calories,
                sugar: JSON.parse(one.nutritions).sugar,
            }
          })
      })
      this.fruits = fruitData;
    })
    .catch((error) => {
      console.log(error)
    })
  },
  watch: {
    favoFruits: {
      handler: function (newVal) {
        if (newVal.length > 10) {
          alert("hey, stop");
          this.favoFruits.pop()
        }
      }
    }
  }
})

</script>
