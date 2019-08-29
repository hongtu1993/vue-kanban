# vue-kanban 

> A drag and drop kanban board component

### [Demo](https://brockreece.github.io/vue-kanban/)

### Installation

Add vue-kanban to your project with npm
``` bash
npm install vue-kanban-mao 
```

... or yarn
```bash
yarn add vue-kanban-mao
```

### Basic Usage

Install the plugin
```js
import vueKanban from 'vue-kanban-mao'

Vue.use(vueKanban)
```

and then use the component in your project.
```html
<kanban-board :stages="stages" :blocks="blocks"></kanban-board>
```

#### Props
- stages: an array of stages for the kanban board
- blocks: an array of objects that will make up the blocks on the kanban board
```js
{
  stages: [{label: 'on-hold', key: 'on-hold'}, {label: 'in-progress', key: 'in-progress'}, {label: 'needs-review', key: 'needs-review'}, {label: 'approved', key: 'approved'}],
  blocks: [
    {
      id: 1,
      status: 'on-hold',
      title: 'Test',
    },
  ],
}
```

### Receiving Changes
The component will emit an event when a block is moved

```html
<kanban-board :stages="stages" :blocks="blocks" @update-block="updateBlock"></kanban-board>
<script>
...
  methods: {
    updateBlock(id, status, index) {
      this.blocks.find(b => b.id === Number(id)).status = status;
    },
  },
...
</script>
```

### Add some style
I have included a scss stylesheet in this repo as a starting point that can be used in your project
```html
<style lang="scss">
  @import './assets/kanban.scss';
</style>
```

### Customize the kanban blocks
Each block has a named slot which can be extended from the parent, like so...
```html
  <kanban-board
      :stages="stages"
      :blocks="blocks"
      @update-block="updateBlock"
      status="status"
      label="label"
      keyValue="key"
    >
      <div v-for="(stage, index) in stages" :key="index">
        <h2>{{ stage["label"] }}</h2>
      </div>
      <div v-for="(stage, index) in stages" :slot="stage['key']" :key="index">
        <h2>{{ stage["label"] }}</h2>
      </div>
      <div v-for="block in blocks" :slot="block['id']" :key="block.id">
        <div><strong>id:</strong> {{ block.id }}</div>
        <div>
          {{ block.title }}
        </div>
      </div>
      <div
        v-for="(stage, index) in stages"
        :key="index"
        :slot="`footer-${stage.key}`"
        style="margin: 10px;text-align: left;color: white;"
      >
        <Form label-position="top">
          <FormItem label="Create" prop="title">
            <input type="hidden" v-model="saveTaskForm[index]['swimlanesId']" />
            <Input
              type="text"
              placeholder="Enter title"
              v-model="saveTaskForm[index]['title']"
            />
          </FormItem>
          <FormItem style="text-align: right;">
            <Button
              type="primary"
              size="small"
              @click="onSubmitSaveTask(saveTaskForm[index])"
              >Submit</Button
            >
          </FormItem>
        </Form>
      </div>
    </kanban-board>
```
