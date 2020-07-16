---
id: example-context
title: Context
sidebar_label: Context
---

```javascript
import React from 'react';
import { Text } from 'react-native';

import { render } from '@testing-library/react-native';
import { NameContext, NameProvider, NameConsumer } from '../react-context';

test('NameConsumer shows default value', () => {
  const { getByText } = render(<NameConsumer />);
  expect(getByText(/^My Name Is:/).props.children.join('')).toBe('My Name Is: Unknown');
});

test('NameConsumer shows value from provider', () => {
  const tree = (
    <NameContext.Provider value="C3P0">
      <NameConsumer />
    </NameContext.Provider>
  );
  const { getByText } = render(tree);
  expect(getByText(/^My Name Is:/).props.children.join('')).toBe('My Name Is: C3P0');
});

test('NameProvider composes full name from first, last', () => {
  const tree = (
    <NameProvider first="Boba" last="Fett">
      <NameContext.Consumer>{(value) => <Text>Received: {value}</Text>}</NameContext.Consumer>
    </NameProvider>
  );
  const { getByText } = render(tree);
  expect(getByText(/^Received:/).props.children.join('')).toBe('Received: Boba Fett');
});

test('NameProvider/Consumer shows name of character', () => {
  const tree = (
    <NameProvider first="Leia" last="Organa">
      <NameConsumer />
    </NameProvider>
  );
  const { getByText } = render(tree);
  expect(getByText(/^My Name Is:/).props.children.join('')).toBe('My Name Is: Leia Organa');
});
```
