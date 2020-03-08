<template>
  <div
    id="detail-modal"
    class="modal fade"
    tabindex="-1"
    role="dialog"
    aria-labelledby="detail-modal-title"
    aria-hidden="true"
  >
    <div class="modal-dialog modal-lg" role="document">
      <div class="modal-content">
        <div class="modal-header">
          <h5 class="modal-title" id="detail-modal-title">Detail</h5>
          <button class="close" data-dismiss="modal" aria-label="Close">
            <span aria-hidden="true">&times;</span>
          </button>
        </div>
        <div class="modal-body">
          <div class="card">
            <img
              class="card-img-top mt-2"
              :src="movieDetail.Poster"
              alt="Poster Film"
              :style="{'width': '400px', 'margin': '0 auto'}"
            />
            <div class="card-body">
              <h5 class="card-title">{{movieDetail.Title}} ({{movieDetail.Year}})</h5>
              <p class="card-text">{{movieDetail.Plot}}</p>
            </div>
            <table class="table text-left">
              <thead class="thead-dark">
                <tr>
                  <th>Name</th>
                  <th>Detail</th>
                </tr>
              </thead>
              <tbody>
                <tr v-for="(item, index) in tableDetail" :key="index">
                  <td>{{item}}</td>
                  <td>{{movieDetail[item]}}</td>
                </tr>
              </tbody>
            </table>
            <ul class="list-group mt-2">
              <li
                class="list-group-item d-flex justify-content-between align-items-center"
                v-for="(item, index) in ratingDetail"
                :key="index"
              >
                {{item.Source}}
                <span class="badge badge-primary badge-pill">{{item.Value}}</span>
              </li>
            </ul>
          </div>
        </div>
        <div class="modal-footer">
          <button type="button" class="btn btn-secondary" data-dismiss="modal">Close</button>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "MovieModal",
  props: ['movieDetail','tableDetail','ratingDetail']
};
</script>

<style>
</style>