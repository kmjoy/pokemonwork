<script setup>
const route = useRoute();
const router = useRouter();
const config = useRuntimeConfig();
const { data: trainer, refresh } = await useFetch(
  () => `/api/trainer/${route.params.name}`,
  {
    default: () => [],
    baseUrl: config.public.backendOrigin,
  },
);
const {
  dialog: deleteDialog,
  onOpen: onOpenDelete,
  onClose: onCloseDelete,
} = useDialog();
//トレーナー削除時、「はい」をクリックしたら、S3からトレーナー情報を削除する
const onDelete = async () => {
  const response = await $fetch(`/api/trainer/${route.params.name}`, {
    baseURL: config.public.backendOrigin,
    method: "DELETE",
  }).catch((e) => e);
  if (response instanceof Error) return;
  router.push("/");
};
</script>

<template>
  <div>
    <h1>トレーナー情報</h1>
    <div class="trainer-info">
      <img src="/avatar.png" />
      <span>{{ trainer.name }}</span>
    </div>
    <GamifyButton @click="onOpenDelete(true)">トレーナー情報を削除し、メイン画面に戻る</GamifyButton>
    <h2>てもちポケモン</h2>
    <CatchButton :to="`/trainer/${route.params.name}/catch`">ポケモンを捕まえる</CatchButton>
    <GamifyDialog
      v-if="deleteDialog"
      id="confirm-delete"
      title="かくにん"
      description="本当にトレーナー情報を削除し、メイン画面に戻って良いか。この操作は取り消せないぞ！"
      @close="onCloseDelete">
      <GamifyList :border="false" direction="horizon">
        <GamifyItem>
          <GamifyButton @click="onCloseDelete">いいえ</GamifyButton>
        </GamifyItem>
        <GamifyItem>
          <GamifyButton @click="onDelete">はい</GamifyButton>
        </GamifyItem>
      </GamifyList>
    </GamifyDialog>
  </div>
</template>

<style scoped>
.item>label {
  display: block;
  margin-bottom: 0.25rem;
}

.gamify-item:hover img {
  animation: bounce;
  animation-duration: 0.8s;
  animation-iteration-count: infinite;
}

.trainer-info {
  display: flex;
  align-items: center;
  gap: 0.5rem;
  margin-bottom: 1rem;
}

.trainer-info>img {
  width: 3rem;
  height: 3rem;
}
</style>
