<template>
  <div class="project-board">
    <div class="project-heading">
      <h1>{{ projectHeading }}</h1>
    </div>
    <div class="outer-box">
      <div v-for="status in statuses" :key="status.id" class="status-column">
        <h2 class="status-title ">{{ status.title }} ({{ getStatusCount(status.id) }})</h2>
        <div class="cards-container" @drop="handleDrop(status.id)" @dragover.prevent>
          <div
            v-for="card in status.cards"
            :key="card.id"
            class="card"
            draggable="true"
            @dragstart="handleDragStart(card.id, status.id)"
          >
            <router-link :to="{ name: 'taskdetails', params: { taskId: card.id } }">
              {{ card.title }}
            </router-link>
          </div>
        </div>
        <button @click="addNewTask(status.id)" class="add-new-button">+ New</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      projectHeading: "Studyboard", // Default heading
      statuses: [
        {
          id: 1,
          title: "Not started",
          cards: [
            { id: 1, title: "Card 1" },
            { id: 2, title: "Card 2" },
          ],
        },
        { id: 2, title: "In progress", cards: [{ id: 3, title: "Card 3" }] },
        {
          id: 3,
          title: "Completed",
          cards: [
            { id: 4, title: "Card 4" },
            { id: 5, title: "Card 5" },
          ],
        },
      ],
    };
  },
  // ... (other methods)

  methods: {
    handleDragStart(cardId, statusId) {
      // Find the card being dragged
      const draggedCard = this.findCard(cardId, statusId);

      // Set the dragged data
      event.dataTransfer.setData(
        "text/plain",
        JSON.stringify({ card: draggedCard, statusId })
      );
    },

    // Add a method to find a card by its ID and status ID
    findCard(cardId, statusId) {
      const status = this.statuses.find((s) => s.id === statusId);
      return status.cards.find((card) => card.id === cardId);
    },

    handleDrop(targetStatusId) {
      // Prevent the default behavior of the drop event
      event.preventDefault();

      // Get the dragged data
      const draggedData = JSON.parse(event.dataTransfer.getData("text/plain"));
      const { card, statusId: sourceStatusId } = draggedData;

      // If the drop target is different from the source, move the card
      if (sourceStatusId !== targetStatusId) {
        this.moveCard(card, sourceStatusId, targetStatusId);
      }
    },

    moveCard(card, sourceStatusId, targetStatusId) {
      // Find the source and target statuses
      const sourceStatus = this.statuses.find((s) => s.id === sourceStatusId);
      const targetStatus = this.statuses.find((s) => s.id === targetStatusId);

      // Remove the card from the source status
      const cardIndex = sourceStatus.cards.findIndex((c) => c.id === card.id);
      sourceStatus.cards.splice(cardIndex, 1);

      // Add the card to the target status
      targetStatus.cards.push(card);
    },
    // Inside the component
    addNewTask(statusId) {
      const newCard = { id: this.generateUniqueId(), title: "New Card" };
      const statusIndex = this.statuses.findIndex(
        (status) => status.id === statusId
      );
      this.statuses[statusIndex].cards.push(newCard);
    },
    getStatusCount(statusId) {
      return (
        this.statuses.find((status) => status.id === statusId)?.cards.length ||
        0
      );
    },
    generateUniqueId() {
      return "_" + Math.random().toString(36).substr(2, 9);
    },
  
  },
};
</script>

<style scoped>
.project-board {
  padding: 20px;
}

.project-heading h1 {
  font-size: 2rem;
  color: #2c3e50; /* Example color: Dark blue-gray */
  margin-bottom: 20px;
}

.outer-box {
  display: flex;
  justify-content: space-between;
  flex-wrap: wrap;
}

.status-column {
  flex: 1;
  width: 300px; /* Adjust the width based on your design */
  margin: 0 10px;
  margin-bottom: 20px;
  padding: 10px;
  background-color: #ecf0f1; /* Example color: Clouds */
  border-radius: 5px;
  box-shadow: 0 2px 4px rgba(0, 0, 0, 0.1);
  transition: box-shadow 0.3s ease-in-out;
}

.status-title {
  font-size: 1.5rem;
  margin-bottom: 10px;
}

.not-started {
  background-color: rgb(157 130 130);
  box-shadow: 0 0 10px rgba(157, 130, 130, 0.7);
}

.in-progress {
  background-color: rgb(211 201 172);
  box-shadow: 0 0 10px rgba(211, 201, 172, 0.7);
}

.completed {
  background-color: rgb(132 145 139);
  box-shadow: 0 0 10px rgba(132, 145, 139, 0.7);
}

.cards-container {
  padding: 10px;
  min-height: 100px;
  margin-bottom: 10px;
}

.card {
  background-color: #fff;
  padding: 15px;
  margin-bottom: 10px;
  border-radius: 8px;
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
  transition: box-shadow 0.3s ease-in-out;
}

.card:hover {
  box-shadow: 0 8px 16px rgba(0, 0, 0, 0.2);
}

.add-new-button {
  background-color: #ecf0f1;
  color: rgba(0, 0, 0, 0.45882);
    font-weight: 500;
    border: none;
    padding: 5px;
    cursor: pointer;
    border-radius: 15px;
    transition: background-color 0.3s ease-in-out;
}

.add-new-button:hover {
  background-color:#00000021; /* Example color: Belize Hole (darkened) */
}
.card {
  border-radius: 8px;
    box-shadow: 0px 0px 5px 1px #0000009e;
}
</style>
