<template>
    <div>
        <div>
            <div class="postHeaderUp">
                <!-- 게시판 제목 -->
                <h2>{{ categoryKor }} 게시판</h2>
                <!-- 게시판 짧은 설명 -->
                <p>{{ categoryDesc }}</p>
            </div>
            <!-- <div v-if="isSinglePost">
                단일 게시글 조회
                <SinglePost :id="singlePostId"/>
            </div> -->
        </div>
        <!-- 검색창 + 글쓰기 버튼 -->
        <div class="postHeaderDown">
            <SearchBox @return-result="showSearchResult"/>
            <button class="blue" onclick="location.href='/post/write'">글쓰기</button>
        </div>
        <!-- 게시글 목록 -->
        <div class="list-card">
            <List :headers="headers" :items="paginatedPosts">
                <template #title="{ value, row }">
                    <router-link :to="`/post/${row.id}`">{{ value }}</router-link>
                </template>
                <template #nickname="{ value, row }">
                    <router-link :to="`/member/${row.member_id}`">{{ value }}</router-link>
                </template>
            </List>
        </div>
        <!-- 페이지네이션 + 페이지 목록 수 콤보박스 -->
        <Pagination class="pagination" v-model:currentPage="currentPage" :totalPages="totalPages" />
    </div>
</template>

<script setup>
    import SearchBox from '@/components/common/SearchBox.vue';
    // import SinglePost from '@/components/post/SinglePost.vue';
    import postMember from '@/json/post_member.json';
    import Pagination from '@/components/common/Pagination.vue'
    import List from '@/components/post/List.vue';
    import postList from '@/json/post_list.json';

    import { ref, computed, watch, onMounted } from 'vue';
    import { useRoute, useRouter } from 'vue-router';

    const currentRoute = useRoute();
    const router = useRouter();
    const currentPage = ref(1)
    const itemsPerPage = 10

    const categoryKor = computed(() => {
        switch (currentRoute.params.category) {
            case 'free': return '자유';
            case 'recruit': return '모집';
            case 'qna': return 'Q&A';
        }
    });

    const categoryDesc = computed(() => {
        switch (currentRoute.params.category) {
            case 'free': return '자유롭게 의견을 나누는 공간입니다.';
            case 'recruit': return '팀원을 모집하는 공간입니다.';
            case 'qna': return '모르는 부분을 물어보는 공간입니다.';
        }
    });

    const headers = ref(['번호', '제목', '작성 시간', '작성자']);
    const items = ref([]);

    // 임시 멤버 데이터
    const memberList = ref(postMember);

    // 백엔드 연동 전 임시 데이터
    onMounted(() => {
        items.value = postList;
    });

    // 백엔드 연동 되어야 정상 작동(테스트 안 됨)
    const showSearchResult = (result) => {
        // 게시글 목록에 전달
        items.value = result;
    };

    watch(() => currentRoute.params.category, () => currentPage.value = 1);

    const categoryCode = computed(() => {
        switch(currentRoute.params.category) {
            case 'free': return 0;
            case 'recruit': return 1;
            case 'qna': return 2;
        }
    });
    
    console.log('categoryCode',categoryCode.value);

    const showablePosts = computed(() => items.value.filter(post => post.status == 0));
    const categorizedShowablePosts = computed(() => 
        showablePosts.value.filter(post => post.category === categoryCode.value)
    );

    const mappedShowablePosts = computed(() => {
        return categorizedShowablePosts.value.map(post => {
            const member = memberList.value.find(m => m.id === post.member_id);
            return {
                id: post.id,
                title: post.title,
                upload_at: post.upload_at,
                nickname: member ? member.nickname : '알 수 없음'
            }
        });
    });

    const sortedShowablePosts = computed(() => {
        return [...mappedShowablePosts.value].sort((a, b) => b.id - a.id);
    });


    const paginatedPosts = computed(() => {
        const start = (currentPage.value - 1) * itemsPerPage;
        return sortedShowablePosts.value.slice(start, start + itemsPerPage);
    });

    const totalPages = computed(() => Math.ceil(sortedShowablePosts.value.length / itemsPerPage))

    const isSinglePost = ref(false);
    const singlePostId = computed(() => currentRoute.params.id);
    const singlePost = computed(() => {
        return singlePostId.value ? items.value.find(post => post.id === Number(singlePostId.value)) : null;
    });
</script>

<style scoped>
    .postHeaderUp {
        margin-left: 30px;
    }

    .postHeaderDown{
        margin-left: 30px;
        margin-right: 30px;
        margin-bottom: 30px;
        display: flex;
        flex-direction: row;
        justify-content: space-between;
        align-items: center;
    }

    button.blue {
        border-radius: 10px;
        background-color: #133286;
        color: white;
        height: 30px;
        width: auto;
        padding-left: 25px;
        padding-right: 25px;
    }

    .list-card {
        background-color: #fff;
        border-radius: 12px;
        box-shadow: 0 4px 12px rgba(0, 0, 0, 0.05);
    }

    .pagination {
        margin-top: 24px;
        display: flex;
        justify-content: center;
    }
</style>