<template>
  <ion-page>
    <ion-header>
      <ion-toolbar color="primary">
        <ion-title>⏳ Hiển thị Thời Gian</ion-title>
      </ion-toolbar>
    </ion-header>

    <ion-content class="ion-padding content-container">
      <div class="button-container">
        <ion-button expand="full" color="success" @click="showCurrentTime">📅 Xem Thời Gian</ion-button>
      </div>

      <ion-card v-if="currentTime" class="time-card animate">
        <ion-card-content>
          <p class="time-text">🕒 <strong>Thời gian hiện tại:</strong> {{ currentTime }}</p>
          <ion-button expand="full" color="secondary" @click="shareTime">📤 Chia sẻ</ion-button>
          <ion-button expand="full" color="tertiary" @click="captureScreenshot">📸 Chụp màn hình</ion-button>
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

const getCurrentTime = () => {
  const now = new Date();
  return now.toLocaleTimeString();
};

const showCurrentTime = async () => {
  currentTime.value = getCurrentTime();
  await LocalNotifications.requestPermissions();
  await LocalNotifications.schedule({
    notifications: [{
      id: 1,
      title: "Thời gian hiện tại",
      body: `Bây giờ là: ${currentTime.value}`,
      schedule: { at: new Date(Date.now() + 1000) }
    }],
  });
};

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
.content-container {
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  height: 100%;
  background: linear-gradient(to bottom, #f0f8ff, #d1e7ff);
}
.button-container {
  width: 90%;
  display: flex;
  justify-content: center;
}
ion-button {
  font-size: 1.1rem;
  font-weight: bold;
  margin-top: 20px;
  width: 90%;
  transition: transform 0.2s ease;
}
ion-button:hover {
  transform: scale(1.05);
}
.time-card {
  margin-top: 30px;
  width: 90%;
  text-align: center;
  border-radius: 15px;
  box-shadow: 0 4px 10px rgba(0, 0, 0, 0.15);
  background: #ffffff;
  animation: fadeIn 0.5s ease-in-out;
}
.time-text {
  font-size: 1.2rem;
  color: #333;
}
@keyframes fadeIn {
  from { opacity: 0; transform: translateY(-10px); }
  to { opacity: 1; transform: translateY(0); }
}
</style>