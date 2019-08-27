<template>
  <div class="drag-container">
    <ul class="drag-list">
      <li v-for="(stage,index) in stages" v-if="stage" class="drag-column" :class="{['drag-column-' + stage[keyValue]]: true}" :key="index">
        <span class="drag-column-header">
          <slot :name="stage[keyValue]">
            <h2>{{ stage[label] }}</h2>
          </slot>
        </span>
        <div class="drag-options"></div>
        <ul class="drag-inner-list" ref="list" :data-status="stage[keyValue]">
          <li class="drag-item" v-if="block" v-for="block in getBlocks(stage[keyValue])" :data-block-id="block.id" :key="block.id">
            <slot :name="block.id">
<!--              <strong>{{ block.status }}</strong>-->
<!--              <div>{{ block.id }}</div>-->
            </slot>
          </li>
        </ul>
        <div class="drag-column-footer">
            <slot :name="`footer-${stage[keyValue]}`"></slot>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
  import dragula from 'dragula';

  export default {
    name: 'KanbanBoard',

    props: {
      keyValue: {
        type: String,
        default: 'key',
      },
      label: {
        type: String,
        default: 'label',
      },
      status: {
        type: String,
        default: 'stat',
      },
      stages: {
        type: Array,
        default: null,
      },
      blocks: {
        type: Array,
        default: null,
      },
    },
    data() {
      return {

      };
    },

    computed: {
      localBlocks() {
        return this.blocks;
      },
    },

    methods: {
      getBlocks(status) {
        const that = this;
        return this.localBlocks.filter(block => block[that.status] === status);
      },
    },

    mounted() {
      const that = this;
      that.$nextTick(() => {
        setTimeout(() => {
          dragula(this.$refs.list)
            .on('drag', (el) => {
              el.classList.add('is-moving');
            })
            .on('drop', (block, list) => {
              let index = 0;
              for (index = 0; index < list.children.length; index += 1) {
                if (list.children[index].classList.contains('is-moving')) break;
              }
              this.$emit('update-block', block.dataset.blockId, list.dataset.status, index);
            })
            .on('dragend', (el) => {
              el.classList.remove('is-moving');

              window.setTimeout(() => {
                el.classList.add('is-moved');
                window.setTimeout(() => {
                  el.classList.remove('is-moved');
                }, 600);
              }, 100);
            });
        }, 500);
      });
    },
  };
</script>
