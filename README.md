# react-native-expo-mobx
React Native with Expo and MobX

## Step 1: Install packages:
```
npm install --save mobx mobx-react
```
## Step 2: Install dev package and config it:

```
npm install --save-dev @babel/plugin-proposal-decorators
```

We need to add this plugin to `babel.config.js` like bellow:

```
module.exports = function(api) {
  api.cache(true);
  return {
    presets: ['babel-preset-expo'],
    plugins: [
      [
        "@babel/plugin-proposal-decorators",
        {
          "legacy": true
        }
      ]
    ]
  };
};

```

## Step 3: Create store and use it:

**TaskListStore.js:**


```

import { observable, action } from 'mobx'

class TaskListStore {

  @observable list = [
    { title: 'Go to the School', isFinished: true },
    { title: 'Prepare tasks for today', isFinished: false },
    { title: 'Team meeting', isFinished: false },
    { title: 'Commit tasks changed', isFinished: false }
  ]

  @action finishItem (index) {
    const copiedList = this.list.slice()
    const isFinished = copiedList[index].isFinished
    if (isFinished) return

    copiedList[index].isFinished = true
    this.list = copiedList // update store by re-assigning
  }

  @action deleteItem (index) {
    this.list = this.list.filter((item, i) => i != index)
  }
}

const store = new TaskListStore()
export default store


```

**TaskListScreen.js:**

```
import React, { Component } from 'react'
import {
  StyleSheet,
  Text,
  TouchableOpacity,
  FlatList,
  View
} from 'react-native'

import taskListStore from './../mobx/TaskListStore'
import { observer } from 'mobx-react/native'

@observer
export default class TaskListScreen extends Component {

  renderItem = ({ item, index }) => {
    return (
      <View style={styles.itemContainer}>
        <View>
          <TouchableOpacity style={{ marginTop: -2 }} onPress={() => taskListStore.finishItem(index)}>
            <Text> {(item.isFinished) ? `✅` : `🕘`} </Text>
          </TouchableOpacity>
        </View>
        <View style={{ flex: 1 }}>
          <Text style={{ color: 'black' }}>{item.title}</Text>
        </View>
        <View style={{ justifyContent: 'center' }}>
          <TouchableOpacity style={{ marginTop: -2 }} onPress={() => taskListStore.deleteItem(index)}>
            <Text>{`❌`}</Text>
          </TouchableOpacity>
        </View>
      </View>
    )
  }

  render() {
    const list = taskListStore.list.slice() // don't forget copy the list from store

    return (
      <FlatList
        style={styles.container}
        data={list}
        extraData={list}
        keyExtractor={(item, index) => `${index}`}
        renderItem={this.renderItem}
      />
    )
  }
}



```

