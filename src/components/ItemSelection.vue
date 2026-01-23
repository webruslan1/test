<template>
  <div class="item-selection">
    <section class="item-selection__section item-selection__section--selected">
      <div class="selection-panel">
        <header class="selection-panel__header">
          <h2 class="selection-panel__title">Выбранные вещи</h2>
          <span class="selection-panel__counter">
            {{ selectedUserItems.length }}/{{ MAX_USER_SELECTION }}
          </span>
        </header>
        <div class="selection-panel__body">
          <div class="selected-items">
            <article
                v-for="item in selectedUserItems"
                :key="item.id"
                class="selected-items__item"
            >
              <h3 class="selected-items__name">{{ item.name }}</h3>
            </article>
          </div>
        </div>
      </div>

      <div class="selection-panel">
        <header class="selection-panel__header">
          <h2 class="selection-panel__title">Выбранная вещь</h2>
        </header>
        <div class="selection-panel__body">
          <div class="selected-single">
            <article
                v-if="selectedAvailableItem"
                class="selected-single__item"
            >
              <h3 class="selected-single__name">
                {{ selectedAvailableItem.name }}
              </h3>
            </article>
          </div>
        </div>
      </div>
    </section>

    <section class="item-selection__section item-selection__section--available">
      <div class="items-panel">
        <header class="items-panel__header">
          <h2 class="items-panel__title">Вещи пользователя</h2>
        </header>
        <div class="items-panel__body">
          <div class="item-grid item-grid--user">
            <button
                v-for="item in userItems"
                :key="item.id"
                :class="[
                'item-card',
                { 'item-card--selected': isUserItemSelected(item.id) },
                { 'item-card--disabled': !isUserItemSelected(item.id) && selectedUserItems.length >= MAX_USER_SELECTION }
              ]"
                :disabled="!isUserItemSelected(item.id) && selectedUserItems.length >= MAX_USER_SELECTION"
                :aria-pressed="isUserItemSelected(item.id)"
                :aria-label="`Выбрать ${item.name}`"
                @click="toggleUserItem(item)"
            >
              <span class="item-card__name">{{ item.name }}</span>
            </button>
          </div>
        </div>
      </div>

      <div class="items-panel">
        <header class="items-panel__header">
          <h2 class="items-panel__title">Вещи на выбор</h2>
        </header>
        <div class="items-panel__body">
          <div class="item-grid item-grid--available">
            <button
                v-for="item in availableItems"
                :key="item.id"
                :class="[
                'item-card',
                { 'item-card--selected': isAvailableItemSelected(item.id) }
              ]"
                :aria-pressed="isAvailableItemSelected(item.id)"
                :aria-label="`Выбрать ${item.name}`"
                @click="selectAvailableItem(item)"
            >
              <span class="item-card__name">{{ item.name }}</span>
            </button>
          </div>
        </div>
      </div>
    </section>
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';

// Constants
const MAX_USER_SELECTION = 6;

// Data
const userItems = ref([
  { id: 1, name: 'Shoes 1' },
  { id: 2, name: 'Shoes 2' },
  { id: 3, name: 'Shoes 3' },
  { id: 4, name: 'Shoes 4' },
  { id: 5, name: 'T-shirt 1' },
  { id: 6, name: 'T-shirt 2' },
  { id: 7, name: 'T-shirt 3' },
  { id: 8, name: 'T-shirt 4' },
]);

const availableItems = ref([
  { id: 11, name: 'Jacket 1' },
  { id: 12, name: 'Jacket 2' },
  { id: 13, name: 'Jacket 3' },
  { id: 14, name: 'Jacket 4' },
  { id: 15, name: 'Hoodie 1' },
  { id: 16, name: 'Hoodie 2' },
  { id: 17, name: 'Hoodie 3' },
  { id: 18, name: 'Hoodie 4' },
]);

// State
const selectedUserItems = ref([]);
const selectedAvailableItem = ref(null);

// Computed - создаём Set для O(1) проверок
const selectedUserItemIds = computed(() =>
    new Set(selectedUserItems.value.map(item => item.id))
);

// Обычные функции для проверки вхождения
const isUserItemSelected = (itemId) => {
  return selectedUserItemIds.value.has(itemId);
};

const isAvailableItemSelected = (itemId) => {
  return selectedAvailableItem.value?.id === itemId;
};

// Methods
const toggleUserItem = (item) => {
  if (!item?.id) return;

  const index = selectedUserItems.value.findIndex(i => i.id === item.id);

  if (index !== -1) {
    selectedUserItems.value.splice(index, 1);
  } else if (selectedUserItems.value.length < MAX_USER_SELECTION) {
    selectedUserItems.value.push(item);
  }
};

const selectAvailableItem = (item) => {
  if (!item?.id) return;

  if (selectedAvailableItem.value?.id === item.id) {
    selectedAvailableItem.value = null;
  } else {
    selectedAvailableItem.value = item;
  }
};
</script>

<style scoped>
.item-selection {
  min-height: 100vh;
  padding: 20px;
  background-color: #fff;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, sans-serif;
}

.item-selection__section {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 20px;
  margin-bottom: 20px;
}

.selection-panel {
  background: #fafafa;
  border: 1px solid #ddd;
  padding: 15px;
}

.selection-panel__header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  margin-bottom: 15px;
  padding-bottom: 10px;
  border-bottom: 1px solid #ddd;
}

.selection-panel__title {
  font-size: 16px;
  font-weight: 600;
  color: #333;
  margin: 0;
}

.selection-panel__counter {
  font-size: 14px;
  color: #666;
}

.selection-panel__body {
  min-height: 120px;
}

.selected-items {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 10px;
}

.selected-items__item {
  background: #fff;
  border: 1px solid #ddd;
  padding: 10px;
}

.selected-items__name {
  font-size: 14px;
  font-weight: 500;
  color: #333;
  margin: 0;
}

.selected-single {
  display: flex;
  align-items: flex-start;
}

.selected-single__item {
  background: #fff;
  border: 1px solid #000;
  padding: 10px;
  width: 100%;
}

.selected-single__name {
  font-size: 14px;
  font-weight: 500;
  color: #333;
  margin: 0;
}

.items-panel {
  background: #fafafa;
  border: 1px solid #ddd;
  padding: 15px;
}

.items-panel__header {
  margin-bottom: 15px;
  padding-bottom: 10px;
  border-bottom: 1px solid #ddd;
}

.items-panel__title {
  font-size: 16px;
  font-weight: 600;
  color: #333;
  margin: 0;
}

.item-grid {
  display: grid;
  gap: 10px;
}

.item-grid--user {
  grid-template-columns: repeat(4, 1fr);
}

.item-grid--available {
  grid-template-columns: repeat(4, 1fr);
}

.item-card {
  background: #fff;
  border: 1px solid #ddd;
  padding: 15px;
  cursor: pointer;
  font-family: inherit;
  text-align: center;
}

.item-card:hover:not(:disabled) {
  border-color: #000;
}

.item-card--selected {
  background: #fff;
  border-color: #000;
}

.item-card--disabled {
  opacity: 0.4;
  cursor: not-allowed;
}

.item-card__name {
  font-size: 14px;
  font-weight: 400;
  color: #333;
}
</style>
