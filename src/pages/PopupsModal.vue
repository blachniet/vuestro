<template>
  <vuestro-container>
    <vuestro-card color="var(--vuestro-green)">
      <template #heading>Modals</template>
    </vuestro-card>

    <vuestro-card>
      <template #description>
        VuestroModal default configuration with a VuestroButton as trigger
      </template>
      <vuestro-button @click="modalOpen1 = true">Open Modal</vuestro-button>
      <vuestro-modal :active.sync="modalOpen1" no-scroll>
        <template #title>VuestroModal</template>
        <template #buttons></template>
        <vuestro-container no-grow>
          <vuestro-dropdown click-to-open>
            <template #button>
              <vuestro-button rounded>
                <template #icon>
                  <vuestro-icon name="car"></vuestro-icon>
                </template>
                Make
              </vuestro-button>
            </template>
            <vuestro-list-button>Acura</vuestro-list-button>
            <vuestro-list-button>Cadillac</vuestro-list-button>
            <vuestro-list-button>Chevy</vuestro-list-button>
            <vuestro-list-button>Dodge</vuestro-list-button>
            <vuestro-list-button>Ford</vuestro-list-button>
            <vuestro-list-button>Honda</vuestro-list-button>
            <vuestro-list-button>Infiniti</vuestro-list-button>
            <vuestro-list-button>Nissan</vuestro-list-button>
            <vuestro-list-button>Lexus</vuestro-list-button>
            <vuestro-list-button>Toyota</vuestro-list-button>
            <vuestro-list-button>Super-loooooooong-title</vuestro-list-button>
          </vuestro-dropdown>
          <vuestro-dropdown click-to-open>
            <template #button>
              <vuestro-button rounded>
                <template #icon>
                  <vuestro-icon name="car"></vuestro-icon>
                </template>
                <span>Model</span>
              </vuestro-button>
            </template>
            <vuestro-list-button>Suburban</vuestro-list-button>
            <vuestro-list-button>Ram</vuestro-list-button>
            <vuestro-list-button>Festiva</vuestro-list-button>
            <vuestro-list-button>Civic</vuestro-list-button>
            <vuestro-list-button>Camry</vuestro-list-button>
          </vuestro-dropdown>
        </vuestro-container>
      </vuestro-modal>
    </vuestro-card>

    <vuestro-card>
      <template #description>
        Close modal when clicking outside by setting the close-on-blur property
      </template>
      <vuestro-button @click="modalOpen2 = true">Open Modal</vuestro-button>
      <vuestro-modal :active.sync="modalOpen2" close-on-blur>
        <template #title>VuestroModal</template>
        <template #buttons></template>
        <vuestro-container column>
          <vuestro-button rounded>
            <template #icon>
              <vuestro-icon name="hospital"></vuestro-icon>
            </template>
            SOS
          </vuestro-button>
        </vuestro-container>
      </vuestro-modal>
    </vuestro-card>

    <vuestro-card>
      <template #description>
        VuestroModal has a "toolbar" slot for adding VuestroButtons to the top right, and a "buttons" slot for adding VuestroButtons to the bottom right.
      </template>
      <vuestro-button @click="modalOpen3 = true">Open Modal</vuestro-button>
      <vuestro-modal :active.sync="modalOpen3" close-on-blur close-text="Done" @after-open="onAfterOpen">
        <template #title>VuestroModal</template>
        <template #toolbar>
          <vuestro-button pill no-border variant="info">
            <template #icon>
              <vuestro-icon name="sync-alt"></vuestro-icon>
            </template>
            Refresh
          </vuestro-button>
        </template>
        <template #footer>
          <vuestro-hr></vuestro-hr>
        </template>
        <template #buttons>
          <vuestro-dropdown click-to-open close-on-content-click>
            <template #button>
              <vuestro-button variant="info">
                <template #icon>
                  <vuestro-icon name="list"></vuestro-icon>
                </template>
                Dropdown Button
              </vuestro-button>
            </template>
            <vuestro-list-button>Item 1</vuestro-list-button>
            <vuestro-list-button>Item 2</vuestro-list-button>
          </vuestro-dropdown>
          <vuestro-button variant="primary">
            <template #icon>
              <vuestro-icon name="download"></vuestro-icon>
            </template>
            Download
          </vuestro-button>
          <vuestro-button variant="warning">
            <template #icon>
              <vuestro-icon name="times"></vuestro-icon>
            </template>
            Cancel
          </vuestro-button>
          <vuestro-button variant="success">
            <template #icon>
              <vuestro-icon name="save"></vuestro-icon>
            </template>
            Save
          </vuestro-button>
        </template>
        <p>Modal content</p>
      </vuestro-modal>
    </vuestro-card>

  </vuestro-container>
</template>

<script>

import Icon from 'vue-awesome/components/Icon';

export default {
  name: 'Popups',
  components: {
    Icon,
  },
  data() {
    return {
      modalOpen1: false,
      modalOpen2: false,
      modalOpen3: false,
    };
  },
	methods: {
		onClick() {
			console.log('click');
		},
		onAfterOpen() {
		  console.log('opened');
		},
	}
};

</script>

<style scoped>

.example-container {
  display: flex;
  align-items: flex-start;
}
.example-container-center {
  display: flex;
  justify-content: center;
}
.example-container.dark:before {
  content: "Example Dark Background";
  position: absolute;
  top: 0;
  left: 0;
  color: white;
}
.example-container.dark {
  padding: 30px 20px 80px 10px;
  background-color: var(--vuestro-popup-bg);
  position: relative;
  justify-content: space-between;
}

.example-vertical-container {
  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

</style>