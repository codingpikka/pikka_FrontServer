<template>
  <div class="notice-list">
    <div class="header">
      <h1>공지사항 목록</h1>
      <router-link :to="{ name: 'NoticeCreate' }" class="add-button">공지사항 등록</router-link>
    </div>
    <table>
      <thead>
        <tr>
          <th>NO</th>
          <th>제목</th>
          <th>등록자</th>
          <th>등록일자</th>
          <th>공지 시작일</th>
          <th>공지 종료일</th>
          <th>공지 상태</th>
          <th>관리</th>
        </tr>
      </thead>
      <tbody>
        <tr v-if="notices.length === 0">
          <td colspan="8">공지사항이 없습니다.</td>
        </tr>
        <tr v-else v-for="(notice, index) in notices" :key="notice.notiId">
          <td>{{ index + 1 }}</td>
          <td>
            <router-link :to="{ name: 'NoticeView', params: { id: notice.notiId } }">
              {{ notice.notiTitle }}
            </router-link>
          </td>
          <td>{{ notice.notiAdminName || "관리자" }}</td>
          <td>{{ formatDate(notice.notiCreateAt) }}</td>
          <td>{{ formatDate(notice.notiStartDate) }}</td>
          <td>{{ formatDate(notice.notiCloseDate) || "-" }}</td>
          <td>{{ notice.isPublic ? "공지중" : "종료" }}</td>
          <td>
            <button @click="editNotice(notice.notiId)" class="edit-button">수정</button>
            <button @click="deleteNotice(notice.notiId)" class="delete-button">삭제</button>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
import axios from "axios";

export default {
  name: "NoticeList",
  data() {
    return {
      notices: [],
    };
  },
  created() {
    this.loadNotices();
  },
  methods: {
    async loadNotices() {
      try {
        const response = await axios.get("http://localhost:8083/api/notice");
        this.notices = response.data;
      } catch (error) {
        console.error("공지사항 목록을 불러오는데 실패했습니다:", error);
        alert("공지사항 목록을 불러오는데 실패했습니다.");
      }
    },
    formatDate(dateString) {
      if (!dateString) return "";
      const options = { year: "numeric", month: "long", day: "numeric" };
      return new Date(dateString).toLocaleDateString("ko-KR", options);
    },
    editNotice(id) {
      this.$router.push({ name: "NoticeEdit", params: { id: id.toString() } });
    },
    async deleteNotice(id) {
      if (confirm("정말로 삭제하시겠습니까?")) {
        try {
          await axios.delete(`http://localhost:8083/api/notice/${id}`);
          this.loadNotices();
          alert("공지사항이 성공적으로 삭제되었습니다.");
        } catch (error) {
          console.error("공지사항 삭제 중 오류 발생:", error);
          alert("공지사항 삭제에 실패했습니다.");
        }
      }
    },
  },
};
</script>

<style scoped>
.notice-list {
  max-width: 1000px;
  margin: 2rem auto;
  padding: 2rem;
  background-color: #ffffff;
  box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
  border-radius: 8px;
}

.header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 1.5rem;
}

.add-button {
  padding: 0.5rem 1rem;
  background-color: #4caf50;
  color: white;
  border: none;
  border-radius: 4px;
  text-decoration: none;
  cursor: pointer;
}

table {
  width: 100%;
  border-collapse: collapse;
}

thead {
  background-color: #f2f2f2;
}

th,
td {
  padding: 0.75rem;
  text-align: center;
  border-bottom: 1px solid #ddd;
}

button {
  padding: 0.5rem 1rem;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  font-size: 0.875rem;
  transition: background-color 0.3s ease;
}

.edit-button {
  background-color: #4caf50;
  color: white;
}

.delete-button {
  background-color: #f44336;
  color: white;
}

.edit-button:hover,
.delete-button:hover {
  opacity: 0.9;
}
</style>