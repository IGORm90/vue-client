<template>
<div class="container w-50">
  <div class="form-block">
    <h2>Add Equipment</h2>
    <form v-on:submit.prevent="submitForm" class="mb-3">
    <div class="mb-3">
      <label for="exampleInputPassword1" class="form-label">Тип оборудования</label>
      <select class="form-select" aria-label="Default select example" v-model="form.equipment_type_id" required>
        <option v-for="option in options" :value="option.id" :key="option.id">
          {{option.name}}
        </option>
      </select>
    </div>
    <div class="mb-3">
      <label for="exampleFormControlInput1" class="form-label">Серийные номер</label>
      <input type="text" class="form-control" id="exampleFormControlInput1" v-model="form.serial_number" required>
    </div>
    <div class="mb-3">
      <label for="exampleFormControlTextarea1" class="form-label">Примечание</label>
      <textarea class="form-control" id="exampleFormControlTextarea1" rows="3" v-model="form.note" required></textarea>
    </div>
    <button type="submit" class="btn btn-primary">Submit</button>
    </form>
    <div class="alert alert-danger" role="alert" v-if="errorMsg">
      {{errorMsg}}
    </div>
    <div class="alert alert-success" role="alert"  v-if="successMsg">
      {{successMsg}}
    </div>
    <h2>Search Equipment</h2>
    <form v-on:submit.prevent="submitSearchForm" class="mb-3">

    <div class="mb-3">
      <label for="exampleFormControlInput1" class="form-label">Поиск</label>
      <input type="text" class="form-control" id="exampleFormControlInput1" v-model="searchString">
    </div>

    <button type="submit" class="btn btn-primary">Search</button>
    </form>


    <table class="table" v-if="searchData.length != 0">
      <thead>
        <tr>
          <th scope="col">id</th>
          <th scope="col">Type id</th>
          <th scope="col">Serial Number</th>
          <th scope="col">note</th>
          <th></th>
          <th></th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(item, index) in searchData" :key="item.id">
          <th scope="row">{{item.id}}</th>
          <td>{{item.equipment_type_id}}</td>
          <td>{{item.serial_number}}</td>
          <td>{{item.note}}</td>
          <td>
            <button type="button" class="btn btn-warning" v-on:click="addToEditForm(index)">edit</button>
          </td>
          <td>
            <button type="button" class="btn btn-danger" v-on:click="deleteEquipment(item.id, index)">delete</button>
          </td>
        </tr>
      </tbody>
    </table>

    
    <form v-on:submit.prevent="submitEditForm" class="mb-3" v-if="editForm.id != ''">
      <h2>Edit Equipment</h2>
    <div class="mb-3">
      <div class="mb-3">
        <label for="exampleFormControlInput1" class="form-label">Id</label>
        <input type="text" class="form-control" id="exampleFormControlInput1" v-model="editForm.id" readonly>
      </div>
      <label for="exampleInputPassword1" class="form-label">Тип оборудования</label>
      <select class="form-select" aria-label="Default select example" v-model="editForm.equipment_type_id">
        <option v-for="option in options" :value="option.id" :key="option.id">
          {{option.name}}
        </option>
      </select>
    </div>
    <div class="mb-3">
      <label for="exampleFormControlInput1" class="form-label">Серийные номер</label>
      <input type="text" class="form-control" id="exampleFormControlInput1" v-model="editForm.serial_number">
    </div>
    <div class="mb-3">
      <label for="exampleFormControlTextarea1" class="form-label">Примечание</label>
      <textarea class="form-control" id="exampleFormControlTextarea1" rows="3" v-model="editForm.note"></textarea>
    </div>
    <button type="submit" class="btn btn-primary">Submit</button>
    </form>
  </div>
</div>
</template>

<script>

import axios from "axios";


export default {
  name: 'HelloWorld',
  props: {
    msg: String
  },
  data() {
    return {
      options: [],
      form: {
        equipment_type_id: '',
        serial_number: '',
        note: ''
      },
      editForm: {
        id: '',
        equipment_type_id: '',
        serial_number: '',
        note: ''
      },
      searchData: [],
      errorMsg: '',
      successMsg: '',
      searchString: ''
    };
  },
  mounted() {
    axios.get("http://test-task/api/equipment-type",{
        headers: {
          Accept: 'application/json',
          Authorization: 'Bearer ' + 'eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJhdWQiOiIyIiwianRpIjoiOGE5MTE4YWRjM2I4ZDA4MjBiYTIwNGIxNDUxYzJkNzNkMmFmNzVmOGMwYWY1Y2EzZGU2Zjg4ZTFkNmJlNzA3OGY0OTlmMTg4MzliNDQwNDMiLCJpYXQiOjE2NTU1NDAzNDAuMjA4MTkxLCJuYmYiOjE2NTU1NDAzNDAuMjA4MTk0LCJleHAiOjE2ODcwNzYzNDAuMTg2ODg3LCJzdWIiOiIxIiwic2NvcGVzIjpbXX0.V0OZ3M0BtKkQ8h-YtCh4pjWxsRypTdJU4glJWCPpriFIEwTOyCGg9ap4eWE78Lh22XBPVBvDLNC8vKztWsWZXvvP47NFoMfTXQ-YqaKygT0s20qYBMQZK0trkfHkcRFaONeXwtCHBwva_knWu_Sb_g28p9BHfOLQAOJOjbdb9BJQoIe6qHxvCl98k3fFKK0GzFrqsIQ1zrVUEj1slDerxx_v3ePepD6sLEcHNU_ixWSNdrzqGf4cePrXqO8aOByFWFagBpeG-VckI44cuIEeQRxtqr4y1zdlkqvKEALN0E5LjbcctWn_zAZUEmvGG9OfpdgVleN7_HwurwjBpW7E0n8HnxLoTmgGfeomclgGUuA_B9-RVMxhAxGB6GtmM1sOMZEf9I5L_fUYLPp3xDnzNMtRabN8EPW1q1gUzjUEebVlny5AI6HDyKy_A20hh0LuViOUSftiaPthBR5P0jp9kzk9E4P6OradWJukzBvk1CEdmJB09uFbPYL8HWKRnQDo0p-nhzus_3NC9iu7Fl_aK0f1ANp43VL7KqcdYv3VRPfHw26FR_usMDOIxk9P5yJd4odTWlHhV0P9mA_ycf4xj45E6nH0VEk1Njx-uNpxEDSJxUt7EFKiOVmgseV8XgokXr9sK-zJ0vig-an3qkR204upBNj1WcQ5yQYr_TqWfec'
        }
      })
      .then(response => {
        this.options = response.data.data;
      })
      .catch(error => {
        alert(error)
      })
  },
  methods:{
        submitSearchForm(){
          const headers = { 
            "Accept": "application/json",
            "Authorization": "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJhdWQiOiIyIiwianRpIjoiNmRjY2FmMjliMTU2NTVmYmM4YjE2MzYzZWViODIwZWNlOTZiM2RhMmVkNGMzN2MxNzYyYTYwYTY0MzJhNDQzZDA0MDkyYmM5ZmEzZDBmMmQiLCJpYXQiOjE2NTU1NjI3OTIuMTQzNjM2LCJuYmYiOjE2NTU1NjI3OTIuMTQzNjQsImV4cCI6MTY4NzA5ODc5Mi4xMzA2OTUsInN1YiI6IjEiLCJzY29wZXMiOltdfQ.XaO2-KmWR4hetF7LdYJZLTuDshH5cy4_iB5JoB16AHI_mlHK1NzDf6KR34XQLm3ORhBlXeTtl42NapxkVMPbI1BYfhlOsasCZjtluHWJuHbEWw0tWuxg-OWyTWcSYfhd_Rv-fvBC8JeIaV_qUCaaPjldaN2xfsnt0Mpm27AiJ3fEYwXcL7ZEPOLrU-W4Bjh08xbO_Gn2lFJ_24Pi6xnNjG9udV-AXoyNPNoLQIvvJKY7ToTBK2-ZNegeUYqL-j4aE5dRJPlfeZYISUBBb6CC3rwGSAl_nxB6RMl7088mLWz1uWzc8vKE0nhxeDCEslsdJH0wxWXWpr6RYHgJFXr26j4vX6zh3Hihu_Oy85AGTqgLPM6sRawCE7I_BVoKe8LQPG32tvaf1fI3GTqhMmCiDkQ6dSr4AKcXTnQIBWnDqj_DEgqXMgk64pIMciIuz6WPiitdbdEMVOeXpEU_cQGsrdHOzRhLQ51FXCuEx-lAX5QgXP7uqlDj1jhUCApM9mps_rypAoODNGAEVOCMaE8CP_6qswmG4_wFyJ8thGZ0r8bzSQdYRdy8CZMi2fc5kEdipYATrkWi0Tt_X3jUrTGifYSpibXmcdxRFDVeLVVHSuBb12E0fv_10BetMOP1_BDmYvzp7lnnqxWu-B53vsrvTG5xLR3fAPi9fcH4DNS9OuQ"
          };
          axios.get("http://test-task/api/equipment?input_text=" + this.searchString, {headers})
            .then((response) => {
                this.searchData = response.data.data;
                console.log(this.searchData);
            })
            .catch((error) => {
              this.errorMsg = error.response.data.message;
            }).finally(() => {
                //Perform action in always
            });
        },

        submitEditForm(){
          const headers = { 
            "Accept": "application/json",
            "Authorization": "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJhdWQiOiIyIiwianRpIjoiNmRjY2FmMjliMTU2NTVmYmM4YjE2MzYzZWViODIwZWNlOTZiM2RhMmVkNGMzN2MxNzYyYTYwYTY0MzJhNDQzZDA0MDkyYmM5ZmEzZDBmMmQiLCJpYXQiOjE2NTU1NjI3OTIuMTQzNjM2LCJuYmYiOjE2NTU1NjI3OTIuMTQzNjQsImV4cCI6MTY4NzA5ODc5Mi4xMzA2OTUsInN1YiI6IjEiLCJzY29wZXMiOltdfQ.XaO2-KmWR4hetF7LdYJZLTuDshH5cy4_iB5JoB16AHI_mlHK1NzDf6KR34XQLm3ORhBlXeTtl42NapxkVMPbI1BYfhlOsasCZjtluHWJuHbEWw0tWuxg-OWyTWcSYfhd_Rv-fvBC8JeIaV_qUCaaPjldaN2xfsnt0Mpm27AiJ3fEYwXcL7ZEPOLrU-W4Bjh08xbO_Gn2lFJ_24Pi6xnNjG9udV-AXoyNPNoLQIvvJKY7ToTBK2-ZNegeUYqL-j4aE5dRJPlfeZYISUBBb6CC3rwGSAl_nxB6RMl7088mLWz1uWzc8vKE0nhxeDCEslsdJH0wxWXWpr6RYHgJFXr26j4vX6zh3Hihu_Oy85AGTqgLPM6sRawCE7I_BVoKe8LQPG32tvaf1fI3GTqhMmCiDkQ6dSr4AKcXTnQIBWnDqj_DEgqXMgk64pIMciIuz6WPiitdbdEMVOeXpEU_cQGsrdHOzRhLQ51FXCuEx-lAX5QgXP7uqlDj1jhUCApM9mps_rypAoODNGAEVOCMaE8CP_6qswmG4_wFyJ8thGZ0r8bzSQdYRdy8CZMi2fc5kEdipYATrkWi0Tt_X3jUrTGifYSpibXmcdxRFDVeLVVHSuBb12E0fv_10BetMOP1_BDmYvzp7lnnqxWu-B53vsrvTG5xLR3fAPi9fcH4DNS9OuQ"
          };
          this.editForm._method = 'PUT';
          axios.post("http://test-task/api/equipment/" +  this.editForm.id, this.editForm, {headers})
            .then((res) => {
                this.successMsg = 'Equipment has been edited';
                this.errorMsg = ''; 
                this.editForm = {
                      id: '',
                      equipment_type_id: '',
                      serial_number: '',
                      note: ''
                    }
            })
            .catch((error) => {
              console.log(error);
              console.log(this.editForm);
              this.errorMsg = error.response.data.message;
              this.successMsg = '';
            }).finally(() => {
                //Perform action in always
            });
        },

        submitForm(){
          const headers = { 
            "Accept": "application/json",
            "Authorization": "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJhdWQiOiIyIiwianRpIjoiNmRjY2FmMjliMTU2NTVmYmM4YjE2MzYzZWViODIwZWNlOTZiM2RhMmVkNGMzN2MxNzYyYTYwYTY0MzJhNDQzZDA0MDkyYmM5ZmEzZDBmMmQiLCJpYXQiOjE2NTU1NjI3OTIuMTQzNjM2LCJuYmYiOjE2NTU1NjI3OTIuMTQzNjQsImV4cCI6MTY4NzA5ODc5Mi4xMzA2OTUsInN1YiI6IjEiLCJzY29wZXMiOltdfQ.XaO2-KmWR4hetF7LdYJZLTuDshH5cy4_iB5JoB16AHI_mlHK1NzDf6KR34XQLm3ORhBlXeTtl42NapxkVMPbI1BYfhlOsasCZjtluHWJuHbEWw0tWuxg-OWyTWcSYfhd_Rv-fvBC8JeIaV_qUCaaPjldaN2xfsnt0Mpm27AiJ3fEYwXcL7ZEPOLrU-W4Bjh08xbO_Gn2lFJ_24Pi6xnNjG9udV-AXoyNPNoLQIvvJKY7ToTBK2-ZNegeUYqL-j4aE5dRJPlfeZYISUBBb6CC3rwGSAl_nxB6RMl7088mLWz1uWzc8vKE0nhxeDCEslsdJH0wxWXWpr6RYHgJFXr26j4vX6zh3Hihu_Oy85AGTqgLPM6sRawCE7I_BVoKe8LQPG32tvaf1fI3GTqhMmCiDkQ6dSr4AKcXTnQIBWnDqj_DEgqXMgk64pIMciIuz6WPiitdbdEMVOeXpEU_cQGsrdHOzRhLQ51FXCuEx-lAX5QgXP7uqlDj1jhUCApM9mps_rypAoODNGAEVOCMaE8CP_6qswmG4_wFyJ8thGZ0r8bzSQdYRdy8CZMi2fc5kEdipYATrkWi0Tt_X3jUrTGifYSpibXmcdxRFDVeLVVHSuBb12E0fv_10BetMOP1_BDmYvzp7lnnqxWu-B53vsrvTG5xLR3fAPi9fcH4DNS9OuQ"
          };
          axios.post("http://test-task/api/equipment", this.form, {headers})
            .then((res) => {
                this.successMsg = 'Equipment has been created'; 
                this.errorMsg = '';
                console.log(this.form);
                this.form.equipment_type_id = '';
                this.form.serial_number = '';
                this.form.note = '';
            })
            .catch((error) => {
              this.errorMsg = error.response.data.message;
              this.successMsg = '';
            }).finally(() => {
                //Perform action in always
            });
        },

        deleteEquipment(id, index){
          const headers = { 
            "Accept": "application/json",
            "Authorization": "Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJhdWQiOiIyIiwianRpIjoiNmRjY2FmMjliMTU2NTVmYmM4YjE2MzYzZWViODIwZWNlOTZiM2RhMmVkNGMzN2MxNzYyYTYwYTY0MzJhNDQzZDA0MDkyYmM5ZmEzZDBmMmQiLCJpYXQiOjE2NTU1NjI3OTIuMTQzNjM2LCJuYmYiOjE2NTU1NjI3OTIuMTQzNjQsImV4cCI6MTY4NzA5ODc5Mi4xMzA2OTUsInN1YiI6IjEiLCJzY29wZXMiOltdfQ.XaO2-KmWR4hetF7LdYJZLTuDshH5cy4_iB5JoB16AHI_mlHK1NzDf6KR34XQLm3ORhBlXeTtl42NapxkVMPbI1BYfhlOsasCZjtluHWJuHbEWw0tWuxg-OWyTWcSYfhd_Rv-fvBC8JeIaV_qUCaaPjldaN2xfsnt0Mpm27AiJ3fEYwXcL7ZEPOLrU-W4Bjh08xbO_Gn2lFJ_24Pi6xnNjG9udV-AXoyNPNoLQIvvJKY7ToTBK2-ZNegeUYqL-j4aE5dRJPlfeZYISUBBb6CC3rwGSAl_nxB6RMl7088mLWz1uWzc8vKE0nhxeDCEslsdJH0wxWXWpr6RYHgJFXr26j4vX6zh3Hihu_Oy85AGTqgLPM6sRawCE7I_BVoKe8LQPG32tvaf1fI3GTqhMmCiDkQ6dSr4AKcXTnQIBWnDqj_DEgqXMgk64pIMciIuz6WPiitdbdEMVOeXpEU_cQGsrdHOzRhLQ51FXCuEx-lAX5QgXP7uqlDj1jhUCApM9mps_rypAoODNGAEVOCMaE8CP_6qswmG4_wFyJ8thGZ0r8bzSQdYRdy8CZMi2fc5kEdipYATrkWi0Tt_X3jUrTGifYSpibXmcdxRFDVeLVVHSuBb12E0fv_10BetMOP1_BDmYvzp7lnnqxWu-B53vsrvTG5xLR3fAPi9fcH4DNS9OuQ"
          };
          axios.post('http://test-task/api/equipment/' + id, {_method: 'DELETE'}, {headers})
            .then(function (response) {
              console.log(index);
              console.log(searchData);
              this.searchData.splice(index, 1);
            })
            .catch(function (error) {
              this.errorMsg = error.response.data.message;
            })
        },

        addToEditForm(index){
          this.editForm.id = this.searchData[index].id;
          this.editForm.equipment_type_id = this.searchData[index].equipment_type_id;
          this.editForm.serial_number = this.searchData[index].serial_number;
          this.editForm.note = this.searchData[index].note;
          console.log(this.editForm);
        }
    }
}
</script>

