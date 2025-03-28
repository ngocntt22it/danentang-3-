<template>
  <ion-page>
    <ion-header>
      <ion-toolbar>
        <ion-title>Hiển thị thời gian</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding">
      <ion-button expand="full" @click="showCurrentTime">Xem thời gian</ion-button>

      <ion-card v-if="currentTime">
        <ion-card-content>
          <p><strong>Thời gian hiện tại:</strong> {{ currentTime }}</p>
          <ion-button expand="full" color="secondary" @click="shareTime">Chia sẻ</ion-button>
          <ion-button expand="full" color="tertiary" @click="captureScreenshot">Chụp màn hình</ion-button>
        </ion-card-content>
      </ion-card>
    </ion-content>
  </ion-page>
</template>

<script setup lang="ts">
import { ref } from 'vue';
import { LocalNotifications } from '@capacitor/local-notifications';
import { Share } from '@capacitor/share';
import { Screenshot } from '@capawesome/capacitor-screenshot';
import {
  IonPage, IonHeader, IonToolbar, IonTitle, IonContent,
  IonButton, IonCard, IonCardContent
} from '@ionic/vue';

const currentTime = ref<string | null>(null);

// Lấy thời gian hiện tại
const getCurrentTime = () => {
  const now = new Date();
  return now.toLocaleTimeString();
};

// Hiển thị thời gian & gửi thông báo
const showCurrentTime = async () => {
  currentTime.value = getCurrentTime();

  await LocalNotifications.requestPermissions();
  await LocalNotifications.schedule({
    notifications: [
      {
        id: 1,
        title: "Thời gian hiện tại",
        body: `Bây giờ là: ${currentTime.value}`,
        schedule: { at: new Date(Date.now() + 1000) }
      },
    ],
  });
};

// Chia sẻ thời gian hiện tại
const shareTime = async () => {
  if (currentTime.value) {
    await Share.share({
      title: "Thời gian hiện tại",
      text: `Thời gian hiện tại: ${currentTime.value}`,
      url: "https://yourapp.com",
      dialogTitle: "Chia sẻ thời gian",
    });
  }
};

// Chụp màn hình (Đã sửa lỗi)
const captureScreenshot = async () => {
  try {
    const { uri } = await Screenshot.take();
    console.log("Ảnh chụp màn hình đã được lưu:", uri);
  } catch (error) {
    console.error("Lỗi khi chụp màn hình:", error);
  }
};
</script>

<style scoped>
ion-content {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100%;
}
ion-button {
  margin-top: 20px;
  width: 80%;
}
ion-card {
  margin-top: 20px;
  width: 80%;
  text-align: center;
}
</style>
