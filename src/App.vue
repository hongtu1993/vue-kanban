<template>
  <div id="app">
    <section class="section">
      <h4>
        Vue adoptation of Ettric's
        <a href="//codepen.io/ettrics/pen/QbPEeg">Codepen</a>
      </h4>
    </section>
    <Kanban :stages="statuses" :blocks="blocks" @update-block="updateBlock" status="status" label="label" keyValue="key">
      <div v-for="(stage,index) in statuses" :slot="stage['key']" :key="index">
        <h2>
          {{ stage['label'] }}
          <a>+</a>
        </h2>
      </div>
      <div v-for="item in blocks" :slot="item.id" :key="item.id">
        <div>
          <strong>id:</strong> {{ item.id }}
        </div>
        <div>
          {{ item.title }}
        </div>
      </div>
      <div v-for="(stage,index) in statuses" :key="index" :slot="`footer-${stage.key}`">
          <a href="" @click.prevent="() => addBlock(stage['key'])">+ Add new block</a>
      </div>
    </Kanban>
  </div>
</template>

<script>
import faker from 'faker';
import { debounce } from 'lodash';
import Kanban from './components/Kanban';

export default {
  name: 'app',
  components: {
    Kanban,
  },
  data() {
    return {
      statuses: [{ label: 'on-hold-label', key: 'on-hold' }, { label: 'in-progress-label', key: 'in-progress' }, { label: 'needs-review-label', key: 'needs-review' }, { label: 'approved-label', key: 'approved' }],
      blocks: [],
    };
  },
  mounted() {
    for (let i = 0; i <= 10; i += 1) {
      this.blocks.push({
        id: i,
        status: this.statuses[Math.floor(Math.random() * 4)].key,
        title: faker.company.bs(),
      });
    }
  },

  methods: {
    updateBlock: debounce((id, status) => {
      this.blocks.find(b => b.id === Number(id)).status = status;
    }, 500),
    addBlock: debounce((stage) => {
      this.blocks.push({
        id: this.blocks.length,
        status: stage,
        title: faker.company.bs(),
      });
    }, 500),
  },
};
</script>

<style lang="scss">
  @import './assets/kanban.scss';

  $on-hold: #FB7D44;
  $in-progress: #2A92BF;
  $needs-review: #F4CE46;
  $approved: #00B961;

  * {
    box-sizing: border-box;
  }

  body {
    background: #33363D;
    color: white;
    font-family: 'Lato';
    font-weight: 300;
    line-height: 1.5;
    -webkit-font-smoothing: antialiased;
  }

  .drag-column {
    .drag-column-header > div {
      width: 100%;
      h2 > a {
        float: right;
      }
    }

    .drag-column-footer > div {
        margin-left: 10px;
        a {
            text-decoration: none;
            color: white;
            &:hover {
                text-decoration: underline;
            }
        }
    }

    &-on-hold {
      .drag-column-header,
      .is-moved,
      .drag-options {
        background: $on-hold;
      }
    }

    &-in-progress {
      .drag-column-header,
      .is-moved,
      .drag-options {
        background: $in-progress;
      }
    }

    &-needs-review {
      .drag-column-header,
      .is-moved,
      .drag-options{
        background: $needs-review;
      }
    }

    &-approved {
      .drag-column-header,
      .is-moved,
      .drag-options {
        background: $approved;
      }
    }
  }

  .section {
    padding: 20px;
    text-align: center;

    a {
      color: white;
      text-decoration: none;
      font-weight: 300;
    }

    h4 {
      font-weight: 400;
      a {
        font-weight: 600;
      }
    }
  }
</style>
