<script setup lang="ts">
import type { Component } from 'vue'
import { ref } from 'vue'
import MainInfo from '../components/main/MainInfo.vue'
import WorkspaceInfo from '../components/workspace/WorkSpaceInfo.vue'
import UserInfo from '../components/main/UserInfo.vue'

const axios = inject('$axios')

const WorkspaceListInfo = useWorkspaceListStore()
const WorkspaceOneInfo = useWorkspaceStore()
const User = useUserStore()
const collectionStore = useCollectionStore()
const route = useRouter()

const isMounted = useMounted()
const currentComponent = ref<Component | null>(MainInfo)// 초기값은 MainInfo 컴포넌트로 설정
// const currentUserComponent = ref<Component | null>(UserInfo)
const workspaceinfo = ref<any>(null) // workspaceInfoOne 데이터를 저장할 ref
const fontColor = ref('#F0F0F0')

// watch(() => WorkspaceOneInfo.workspaceInfo.color, () => {
//   // if (newWorkspaceOne) {
//   //   Object.keys(dropdownData.value).forEach((uuid) => {
//   //     dropdownData.value[uuid].isOpen = false
//   //   })
//   //   try {
//   //     const res = await axios.get(`/api/v1/workspaces/members/${newWorkspaceOne.key}`)
//   //     // console.log(res)
//   //     memberInfo.memberInfoList = res.data
//   //   }
//   //   catch (error) {
//   //     console.error('memberList 가져오기 실패 : ', error)
//   //   }
//   // }
//   // // console.log('iwejfopjwefopjweopopwnerw[voerokopr]')

//   // // console.log(WorkspaceOneInfo.workspaceInfo)
//   // changeBoxColor(WorkspaceOneInfo.workspaceInfo?.color)
//   if (WorkspaceOneInfo.workspaceInfo.color === '#C9C9C9' || WorkspaceOneInfo.workspaceInfo.color === '#White')
//     fontColor.value = '#F0F0F0'
//   else
//     fontColor.value = 'black'
// })

function truncateText(text: string, maxLength: number) {
  if (!text)
    return '' // 또는 다른 기본값을 반환할 수 있습니다.

  if (text.length > maxLength)
    return `${text.slice(0, maxLength)}`

  else
    return text
}

function showInfoComponent(workspaceInfoOne: any, index) {
  currentComponent.value = WorkspaceInfo // WorkspaceInfo 컴포넌트로 변경
  WorkspaceOneInfo.workspaceInfo = workspaceInfoOne
  WorkspaceOneInfo.selectedWorkspaceIndex = index
  collectionStore.request = null
}

async function addWorkSpace() {
  try {
    const Userdata = {

      memberList: [
        {
          uuId: User.userInfo?.uuid,
          userPermission: 'admin',
        },

      ],
      name: 'My  workspace',
    }

    // axios를 사용하여 서버에 POST 요청을 보냅니다.
    const response = await axios.post(`/api/v1/workspaces`, Userdata)

    // 요청이 성공하면 WorkspaceList에 새로운 데이터를 추가합니다.
    WorkspaceListInfo.WorkspaceList.push(response)
    const currentRoute = route.currentRoute.value.path

    // 조건에 따라 다른 동작 수행
    if (currentRoute === '/main') {
      // 현재 라우트 위치가 /main인 경우
      route.go(0)
    }
    else {
      // 현재 라우트 위치가 /main이 아닌 경우
      // window.location.reload()
      // route.go(0)
      route.push('/main')
    }
  }
  catch (error) {
    console.error('Error adding workspace:', error)
  }
}
</script>

<template>
  <div w-18 border-r-2 class="list">
    <div v-for="(workspace, index) in WorkspaceListInfo.WorkspaceList" :key="workspace.name" class="box" :style="{ backgroundColor: workspace.color }">
      <div id="workSpaceListData" class="workspaceId" @click="showInfoComponent(workspace, index)">
        {{ truncateText(workspace.name, 4) }}
      </div>
    </div>
    <div class="plus-box cross" @click="addWorkSpace()" />
  </div>
</template>

<style scoped>
.mid {
  height: calc(100% - 70px);
}

.list{
  /* border-color: #B6B6B6; */
  display: flex; /* 부모 요소를 플렉스 컨테이너로 설정 */
  align-items: center;
  flex-direction: column;
}
.box{
  margin-top: 5px ;
  border-radius: 10px;
  width: 50px;
  height: 50px;
  border: 2px solid #000; /* 테두리 스타일 및 색상 설정 */
  background-color:#0F4C81; /* 배경색 설정 */
  color:#F0F0F0;
  cursor: pointer;

}
.plus-box{
  margin-top: 5px ;
  border-radius: 10px;
  width: 50px;
  height: 50px;
  border: 2px solid #000; /* 테두리 스타일 및 색상 설정 */
  background-color :#658DC6; /* 배경색 설정 */
  color:#F0F0F0;
  cursor: pointer;

}

.workspaceId{
  text-align: center; /* 텍스트 가운데 정렬 */
    line-height: 50px; /* 텍스트를 수직 중앙으로 정렬 */
  font-size: 14px;

}

.cross {
  position: relative;
}

.cross::before,
.cross::after {
  content: '';
  position: absolute;
  background-color: #F0F0F0; /* 바의 색상을 설정하세요. */
}

.cross::before {
  width: 2px;
  height: 50%;
  top: 25%;
  left: 50%;
  transform: translateX(-50%);
  border-radius: 50px
}

.cross::after {
  width: 50%;
  height: 2px;
  top: 50%;
  left: 25%;
  transform: translateY(-50%);
  border-radius: 50px

}

.workspaceId:hover {
  cursor: pointer; /* 호버 시 커서를 손가락 아이콘으로 변경 */
}
.active {
  border-color: #000;
}
</style>
