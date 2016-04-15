# Testing asynchronous updates in React components

Suppose your component changes inside `componentDidMount`. Moreover, suppose that this happens asynchronously inside of a Promise.

```javascript
class Delayed extends React.Component {
  constructor(props) {
    super(props)
    this.state = {
      foo: 'bar'
    }
  }

  componentDidMount() {
    this.props.asyncThing()
    .then(res => {
      this.setState({
        foo: res.foo
      })
    })
  }

  render() {
    return <div>{this.state.foo}</div>
  }  
}
```

Here, I made `asyncThing` a prop to make it easier to mock. My test setup uses Enzyme, Mocha, JSDOM, Chai(-as-Promised), and Sinon(-as-Promised):

```javascript
// Not shown: setting up JSDOM
// All this stuff is in the background
import chai, { expect } from 'chai'
import chaiAsPromised from 'chai-as-promised'
chai.use(chaiAsPromised)
import sinon from 'sinon'
import 'sinon-as-promised'
```

Here's how I would write a spec to check that my component updates with the data sent through the Promise.

```javascript
import React from 'react'
import { mount } from 'enzyme'

describe('Delayed', () => {
  it('updates after the promise resolves' () => {
    // Mock asyncThing with sinon-as-promised
    const mock = sinon.stub().resolves({ foo: 'hello' })
    // Mount Delayed
    const comp = mount(<Delayed asyncThing={mock} />)
    // This function will execute after mock is resolved and return data
    // from the future state of the component
    const checker = () => {
      return comp.state().foo === 'hello'
    }
    // Invoke mock to get a reference to the promise, then use chai-as-promised
    // to set an expectation on its eventual value.
    return expect(mock().then(checker)).to.eventually.be.true
  })
})
```
