# React-Native: Keyboard Avoid View

```jsx
import React from 'react'
import PropTypes from 'prop-types'
import {
  View,
  StyleSheet,
  ScrollView,
  UIManager,
  TextInput
} from 'react-native'
import KeyboardSpacer from 'react-native-keyboard-spacer'

class KeyboardHandler extends React.Component {
  static propTypes = {
    children: PropTypes.node.isRequired
  }

  constructor(props) {
    super(props)

    // it's not used for this component
    // but maybe it will be usefull for you =)
    this.state = {
      keyboardIsShowing: false,
      keyboardHeight: 0
    }

    this._onKeyboardToggle = this._onKeyboardToggle.bind(this)
  }

  _onKeyboardToggle(keyboardIsShowing, keyboardHeight) {
    this.setState({
      keyboardIsShowing,
      keyboardHeight
    }, () => {
      this._scrollToFocusedInput()
    })
  }

  _scrollToFocusedInput() {
    const focused = this._currentFocusedInput

    if (!focused) return

    UIManager.measureLayout(
      focused,
      this._scrollView.getInnerViewNode(),
      () => { },
      (originX, originY, width, height) => {
        const targetY = originY - height - 20

        if (targetY < 0) return

        this._scrollView.scrollTo({
          y: targetY
        })
      })
  }

  get _currentFocusedInput() {
    const { State: TextInputState } = TextInput
    const currentlyFocusedField = TextInputState.currentlyFocusedField();

    return currentlyFocusedField
  }

  render() {
    return (
      <View style={styles.container}>
        <ScrollView style={styles.container} ref={ref => this._scrollView = ref}>
          <View style={styles.container}>
            {this.props.children}
            <KeyboardSpacer onToggle={this._onKeyboardToggle} />
          </View>
        </ScrollView>
      </View>
    )
  }
}

const styles = StyleSheet.create({
  container: {
    flex: 1
  }
})

export default KeyboardHandler
```