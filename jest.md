What is it like to use a snapshot test ?

The process for snapshot testing is a bit different than normal tests. Most snapshot tests look fairly simple. Something like this example from the Jest repository:

// Link.react.js
// Copyright 2004-present Facebook. All Rights Reserved.

import React from 'react';

const STATUS = {
  NORMAL: 'normal',
  HOVERED: 'hovered',
};

export default class Link extends React.Component {

  constructor() {
    super();

    this._onMouseEnter = this._onMouseEnter.bind(this);
    this._onMouseLeave = this._onMouseLeave.bind(this);

    this.state = {
      class: STATUS.NORMAL,
    };
  }

  _onMouseEnter() {
    this.setState({class: STATUS.HOVERED});
  }

  _onMouseLeave() {
    this.setState({class: STATUS.NORMAL});
  }

  render() {
    return (
      <a
        className={this.state.class}
        href={this.props.page || '#'}
        onMouseEnter={this._onMouseEnter}
        onMouseLeave={this._onMouseLeave}>
        {this.props.children}
      </a>
    );
  }

}

// Link.react-test.js  (partial)
// Copyright 2004-present Facebook. All Rights Reserved.
/* eslint-disable no-unused-vars */

'use strict'

import React from 'react';
import Link from '../Link.react';
import renderer from 'react-test-renderer';

it('renders correctly', () => {
  const tree = renderer.create(
    <Link page="http://www.facebook.com">Facebook</Link>
  ).toJSON();
  expect(tree).toMatchSnapshot();
});

The first time the test is run, a snapshot file is produced. In this case, running the previous test produces a snapshot file that looks like this:

// Link.react-test.js.snap  (partial)
exports[`test renders correctly 1`] = `
<a
  className="normal"
  href="http://www.facebook.com"
  onMouseEnter={[Function bound _onMouseEnter]}
  onMouseLeave={[Function bound _onMouseLeave]}>
  Facebook
</a>
`;
This gives us a baseline for what we expect the UI to look like. The snapshot is generated in a folder in your __tests__ directory, so that it can be checked into source control.

The next time a test is run, if nothing has changed, the test passes. However, if we change something (let’s say we add a class), the test fails and shows us a diff.

// updated link render method

render() {
  return (
    <a
      className={`link-item ${this.state.class}`}
      href={this.props.page || '#'}
      onMouseEnter={this._onMouseEnter}
      onMouseLeave={this._onMouseLeave}>
      {this.props.children}
    </a>
  );
}

# jest output

FAIL  __tests__/Link.react-test.js
 ✕ renders correctly (16ms)

● renders correctly

    Received value does not match the stored snapshot 1.

    - Snapshot
    + Received

      <a
-       className="normal"
+       className="link-item normal"
        href="http://www.facebook.com"
        onMouseEnter={[Function bound _onMouseEnter]}
        onMouseLeave={[Function bound _onMouseLeave]}>
        Facebook
      </a>

      at Object.<anonymous> (__tests__/Link.react-test.js:14:16)
We can then choose to accept this change by running jest -u to update the snapshot, or update our code to fix the regression. If we update the snapshot file, the test will start passing again.

Snapshot tests are a complement for conventional tests not a replacement

The first thing that became clear to me while using snapshot testing is that they’re not for everything. They are optimized for a different case than normal assertion-based tests.

Classic assertion based tests are perfect for testing clearly defined behavior that is expected to remain relatively stable.

Snapshot tests are great for testing less clearly defined behavior that may change often.

Snapshot testing is perfect for things that in the past would have raised concern for “overly brittle” tests that slow development teams down. The classic example of this (and the main focus of discussion around Jest snapshots so far) is UI testing.

UI components often change in small and trivial ways. Copy is changed, whitespace is added, a border color is modified. Generally this means that developers have to choose between detailed tests that catch any regression but require constant updates, or less detailed tests that focus on core behaviors but miss smaller regressions.

Snapshot tests provide a new way of approaching these problems in unit tests. Because they are easy to write and effortless to update, and provide a clear view of what changed after each update, whether it is major or minor.

This isn’t without any disadvantage. Snapshot tests preserve a starting point, but don’t give any indication of developer intent to future developers. Like any other test, if the initial snapshot passes with bugs, or doesn’t capture the full range of cases, the test won’t adequately cover those behaviors. But unlike traditional tests, snapshot tests don’t provide specific guidance for what the original developer expected beyond the “actual behavior”. If there are multiple sub-behaviors that interact to produce output, its not clear which of those are essential, and which are incidental. That’s not something you want for your critical application logic, especially complicated logic with many subtle cases. But for UI components and other application elements that are likely to change a lot over time, this is a good set of tradeoffs.

Snapshot tests are more useful with a healthy code review process

I’ve pointed out several times now that snapshot tests are easy to update. One of the downsides of the current design is that they can be almost too easy to update. When a snapshot test fails, you have the option of modifying the output so that the test passes again, or updating the snapshot to reflect the new output. Unfortunately, Jest currently provides no granularity for this updating. Running jest -u to update the snapshots will update all snapshots. So if a single change causes many snapshots to break, it can be easy to accidentally update all of them, and possibly difficult to work through the whole diff to find changes.

A robust code review process minimizes these problems. Because snapshots are checked into source, the diffs in the snapshots are checked into source control. Viewing diffs in a dedicated code review tool like Github, Bitbucket or Gitlab, it’s easier to see exactly what changed over many files than it might be when updating snapshots from the command line, and if you update a snapshot multiple times in a single branch, you can see the net effect on the snapshot through source control diffs better than you can through Jest.

Snapshot tests work well with auto-mocking

Auto-mocking has been one of Jest’s more controversial features. Prior to version 15, Jest mocked every module used in tests by default. To run the actual module code, it was necessary to manually unmock each module used or change Jest configurations. Though it is now disabled by default, auto-mocking actually works very well with snapshot tests. One problem with tests that rely on generating DOM in React is the fact that most components of any complexity are actually made up of smaller components, that often use external functions with complex logic to determine what to show. If we fully render everything, changing any of those pieces can break tests for many components, moving us away from the concept of a “unit test”. If we mock child components and helper functions and then test them individually though, we can focus our tests to only change based on the logic of the individual component being tested. This is incredibly easy with Jest, and especially helpful in large code bases that don’t have complete test coverage. Auto-mocking is considered an “advanced feature” of Jest, but it’s not hard to use. Most mocked modules produce something non-harmful by default, and when its necessary to override, its not difficult to produce something meaningful without getting too involved. Just add a file with the same name as the file to be mocked into a __mocks__ folder in that directory, and give the mocked module the same interface as the mocked component. You can either write it from scratch or use jest.genMockFromModule as a starting place.

Summary

I’m impressed by Jest snapshots. When combined with React, they’re providing my first non-frustrating experience unit testing UI components. I’m excited to use them more going forward, and will be interested to see how they evolve from here.

## source
[Testing with Jest Snapshots: First Impressions](https://benmccormick.org/2016/09/19/testing-with-jest-snapshots-first-impressions/)