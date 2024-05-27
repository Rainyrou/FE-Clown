```jsx
class MyComponent extends React.Component {
  state = { count: 0 };

  updateStateMultipleTimes = async () => {
    await this.setStateAsync({ count: this.state.count + 1 });
    console.log(this.state.count); 
    await this.setStateAsync({ count: this.state.count + 1 });
    console.log(this.state.count); 
  };

  setStateAsync(state) {
    return new Promise(resolve => this.setState(state, resolve));
  }

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.updateStateMultipleTimes}>Increment Twice</button>
      </div>
    );
  }
}
```