---
id: version-2.0.0-example-navigation
title: Navigation
sidebar_label: Navigation
original_id: example-navigation
---

```javascript
import React from 'react';
import { Button, Text, View } from 'react-native';
import { createStackNavigator, createAppContainer, withNavigation } from 'react-navigation';

import { render, fireEvent } from ' native-testing-library';

jest.mock('NativeAnimatedHelper').mock('react-native-gesture-handler', () => {
  const View = require('react-native/Libraries/Components/View/View');
  return {
    State: {},
    PanGestureHandler: View,
    BaseButton: View,
    Directions: {},
  };
});

console.warn = (arg) => {
  const warnings = [
    'Calling .measureInWindow()',
    'Calling .measureLayout()',
    'Calling .setNativeProps()',
    'Calling .focus()',
    'Calling .blur()',
  ];

  const finalArgs = warnings.reduce((acc, curr) => (arg.includes(curr) ? [...acc, arg] : acc), []);

  if (!finalArgs.length) {
    console.warn(arg);
  }
};

const Home = ({ navigation }) => (
  <View>
    <Text testID="title">Home page</Text>
    <Button title="About page" onPress={() => navigation.navigate('About')} />
  </View>
);
const About = ({ navigation }) => (
  <View>
    <Text testID="title">About page</Text>
    <Button title="About page" onPress={() => navigation.navigate('Home')} />
  </View>
);
const Location = () => (
  <View>
    <Text testID="title">Location page</Text>
    <LocationDisplay />
  </View>
);

const LocationDisplay = withNavigation(({ navigation }) => (
  <Text testID="location-display">{navigation.state.routeName}</Text>
));

function renderWithNavigation({ screens = {}, navigatorConfig = {} } = {}) {
  const AppNavigator = createStackNavigator(
    {
      Home,
      About,
      Location,
      ...screens,
    },
    { initialRouteName: 'Home', ...navigatorConfig },
  );

  const App = createAppContainer(AppNavigator);

  return { ...render(<App />), navigationContainer: App };
}

test('full app rendering/navigating', async () => {
  const { findByText, getByTestId, getByText } = renderWithNavigation();
  expect(getByTestId('title').props.children).toMatch('Home page');
  fireEvent.press(getByText(/About page/i));
  await expect(findByText('About page')).toBeTruthy();
});

test('rendering a component that uses withNavigation', () => {
  const initialRouteName = 'Location';
  const { getByTestId } = renderWithNavigation({
    navigatorConfig: { initialRouteName },
  });
  expect(getByTestId('location-display').props.children).toBe(initialRouteName);
});
```
