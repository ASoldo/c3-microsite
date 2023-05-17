<template>
  <!-- APP START -->
  <div class="dark min-h-screen flex flex-col">
    <!-- SIDE NAV START -->
    <aside v-if="drawer" class="w-64 bg-gray-800 text-white fixed h-full overflow-auto pt-16">
      <ul class="space-y-2 p-4">
        <li><button @click="setGame1()"
            class="w-full py-2 px-4 bg-blue-500 hover:bg-blue-700 text-white">ComboTap</button>
        </li>
        <li><button @click="setGame2()"
            class="w-full py-2 px-4 bg-blue-500 hover:bg-blue-700 text-white">ComboTap</button>
        </li>
      </ul>
    </aside>
    <!-- SIDE NAV END -->

    <!-- NAVIGATION START -->
    <header class="w-full bg-gray-800 text-white fixed top-0 left-0 py-4 px-8 flex items-center space-x-4">
      <button @click.stop="drawer = !drawer" class="bg-blue-500 hover:bg-blue-700 text-white px-4 py-2 rounded-lg">
        <svg xmlns="http://www.w3.org/2000/svg" fill="none" viewBox="0 0 24 24" stroke="currentColor" class="h-6 w-6">
          <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M4 6h16M4 12h16m-7 6h7"></path>
        </svg>
      </button>
      <div>
        <strong>Score: {{ Score }}</strong>
      </div>
      <div>
        <strong>UserName: {{ UserName }}</strong>
      </div>
      <div>
        <strong>UserEmail: {{ UserEmail }}</strong>
      </div>
      <div>
        <strong>TimeInGame: {{ timeSpentInGame }}</strong>
      </div>
    </header>
    <!-- NAVIGATION END -->

    <!-- GAME CONTAINER START -->
    <main class="flex-1 flex overflow-auto">
      <div class="w-full h-screen">
        <iframe ref="game" crossorigin="anonymous" :src="gameSrc" frameborder="0" class="w-full h-full mx-15"></iframe>
      </div>
    </main>
    <!-- GAME CONTAINER END -->

    <!-- FOOTER START -->
    <footer class="w-full bg-gray-800 text-white fixed bottom-0 left-0 py-4 px-8">
      <span>&copy; <a href="https://rootster.xyz" class="text-white">RootsterGG</a></span>
    </footer>
    <!-- FOOTER END -->
  </div>
  <!-- APP END -->
</template>


<script lang="ts" setup>
import { ref, onMounted, onUnmounted } from 'vue';

// Data
const Score = ref<number>(0);
const HighScore = ref<number>(11);
const UserName = ref<string>("User");
const UserEmail = ref<string>("user@user.com");
const UsersData = ref<string>("");
const PickupLogo = ref<number>(1);
const pickupLogoInput = ref<number>(0);
const OverallHighScore = ref<number>(15);
const UsersDataInput = ref<string>('User1-200,User2-150,User3-100,User4-50');
const scoreInput = ref<string>('');
const nameInput = ref<string>('');
const highScoreInput = ref<string>('');
const overallHighScoreInput = ref<string>('');
const drawer = ref<boolean | null>(false);
const gameSrc = ref<string>("http://localhost:4040/");
const timeSpentInGame = ref<number>(0);

const game = ref<null | HTMLIFrameElement>(null);


// Methods
// const processMessage = (e: MessageEvent) => {
//   // ... code here ...
// };
const processMessage = (e: MessageEvent) => {
  if (e.data.message === "EndGame") {
    console.log(e.data);
    // HERE WE SIMPLY SET VALUES
    Score.value = e.data.payload.Score;
    // this.HighScore = e.data.payload.HighScore;
    // this.UserName = e.data.payload.UserName;
    // this.UserEmail = e.data.payload.Email;
    // this.timeSpentInGame = e.data.payload.TimeSpentInGame;
    // this.OverallHighScore = e.data.payload.OverallHighScore;
  }
  //HERE WE CHECK WHEN GAME SEND SIGNAL TO FRONTEND AND THEN FRONTEND IS INITIALIZING VALUES
  if (e.data.message === "Signal" && game.value?.contentWindow != null) {
    game.value?.contentWindow.postMessage({
      message: 'Signal',
      payload: {
        UserName: UserName.value,
        PersonalHighscore: HighScore.value,
        OverallHighScore: OverallHighScore.value
      }
    }, '*');
  }
  if (e.data.message === "GetUsersData" && game.value?.contentWindow != null) {
    game.value?.contentWindow.postMessage({
      message: 'GetUsersData',
      payload: {
        UsersData: UsersData.value
      }
    }, '*');
  }
}

const setGame1 = () => {
  gameSrc.value = "http://localhost:4040/";
};

const setGame2 = () => {
  gameSrc.value = "http://localhost:4040/";
};

// Lifecycle hooks
onMounted(() => {
  window.addEventListener("message", processMessage);
  console.log('Add event listener', processMessage);
});

onUnmounted(() => {
  window.removeEventListener('message', processMessage);
  console.log('Removed event listener', processMessage);
});
</script>



