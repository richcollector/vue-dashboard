<template>
  <div>서비스 페이지</div>
</template>

<script>
export default {
  data() {},
  mounted() {
    this.counselingGetList();
  },
  methods: {
    counselingGetList() {
      this.requestBody = {
        ServiceKey:
          "NjYn2Py%2BZY59COKW4ae9%2BtcgqOa9f6uFNvZXBN4yCcm0ZznKjLu1uj8pbcuRRrxtJK%2FdRswwCSK%2BJWjkHqTCuQ%3D%3D",
        pageNo: 1,
        numOfRows: 10,
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
          console.log("row", row[0]);
          console.log("res::", res.data);
        })
        .catch((err) => {
          console.error("err::", err);
        });
    },
  },
};
</script>
