<template>
  <div class="board-list">
    <table class="w3-table-all">
      <thead>
        <tr>
          <th>순서</th>
          <th>연도</th>
          <th>기관</th>
          <th>상담 서비스 분야</th>
          <th>상담 수</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="(row, idx) in list" :key="idx">
          <td>{{ row.seq }}</td>
          <td>
            {{ row.wrttimeid }}
          </td>
          <td>{{ row.competent_authority }}</td>
          <td>{{ row.service_content }}</td>
          <td>{{ row.use_amt }}</td>
        </tr>
      </tbody>
    </table>
    <div
      class="pagination w3-bar w3-padding-16 w3-small"
      v-if="paging.total_list_cnt > 0"
    >
      <span class="pg">
        <a
          href="javascript:;"
          @click="fnPage(1)"
          class="first w3-button w3-border"
          >&lt;&lt;</a
        >
        <a
          href="javascript:;"
          v-if="paging.start_page > 10"
          @click="fnPage(`${paging.start_page - 1}`)"
          class="prev w3-button w3-border"
          >&lt;</a
        >
        <template v-for="(n, index) in paginavigation()">
          <template v-if="paging.page == n">
            <strong class="w3-button w3-border w3-green" :key="index">{{
              n
            }}</strong>
          </template>
          <template v-else>
            <a
              class="w3-button w3-border"
              href="javascript:;"
              @click="fnPage(`${n}`)"
              :key="index"
              >{{ n }}</a
            >
          </template>
        </template>
        <a
          href="javascript:;"
          v-if="paging.total_page_cnt > paging.end_page"
          @click="fnPage(`${paging.end_page + 1}`)"
          class="next w3-button w3-border"
          >&gt;</a
        >
        <a
          href="javascript:;"
          @click="fnPage(`${paging.total_page_cnt}`)"
          class="last w3-button w3-border"
          >&gt;&gt;</a
        >
      </span>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      requestBody: {}, //리스트 페이지 데이터전송
      list: {}, //리스트 데이터
      paging: {
        start_page: 0,
        end_page: 0,
        page: 0,
        total_list_cnt: 0,
        total_page_cnt: 0,
      }, //페이징 데이터
      page: this.$route.query.page ? this.$route.query.page : 1,
      size: 10,
      paginavigation: function () {
        let pageNumber = []; //;
        let start_page = this.paging.start_page;
        let end_page = this.paging.end_page;
        for (let i = start_page; i <= end_page; i++) pageNumber.push(i);
        return pageNumber;
      },
    };
  },
  mounted() {
    this.counselingGetList();
  },
  methods: {
    counselingGetList() {
      this.requestBody = {
        ServiceKey:
          "NjYn2Py+ZY59COKW4ae9+tcgqOa9f6uFNvZXBN4yCcm0ZznKjLu1uj8pbcuRRrxtJK/dRswwCSK+JWjkHqTCuQ==",
        pageNo: this.page,
        numOfRows: this.size,
      };

      this.$axios
        .get(this.$serverUrl + "/getConsultationService", {
          params: this.requestBody,
          headers: {},
        })
        .then((res) => {
          let parser = new DOMParser();
          let xmlDoc = parser.parseFromString(res.data, "text/xml");
          let row = xmlDoc.getElementsByTagName("row");

          const list = [];
          for (let i = 0; i < row.length; i++) {
            list.push({
              seq: row[i].querySelector("seq").innerHTML,
              wrttimeid: row[i].querySelector("wrttimeid").innerHTML,
              competent_authority: row[i].querySelector("competent_authority")
                .innerHTML,
              service_content:
                row[i].querySelector("service_content").innerHTML,
              use_amt: row[i].querySelector("use_amt").innerHTML,
            });
          }

          let total_list_cnt = xmlDoc
            .getElementsByTagName("head")[0]
            .querySelector("totalCount").innerHTML;

          this.list = list;
          this.paging.total_list_cnt = total_list_cnt;
          this.paging.page = xmlDoc
            .getElementsByTagName("head")[0]
            .querySelector("pageNo").innerHTML;
          this.paging.total_page_cnt = Math.ceil(total_list_cnt / 10);
          this.paging.start_page = Math.floor(this.paging.page / 5) * 5 + 1;
          this.paging.end_page =
            Math.floor(this.paging.page / 5) * 5 +
            (this.paging.total_page_cnt % 5);
        })
        .catch((err) => {
          console.error("err::", err);
        });
    },
    fnPage(n) {
      if (this.page !== n) {
        this.page = n;
        this.counselingGetList();
      }
    },
  },
};
</script>
