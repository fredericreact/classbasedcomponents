# basic component

Function version

```javascript
import classes from './User.module.css';
 
const User = (props) => {
  return <li className={classes.user}>{props.name}</li>;
};
 
export default User;
```

Class version

```javascript
import { Component } from 'react';
 
import classes from './User.module.css';
 
class User extends Component {
  render() {
    return <li className={classes.user}>{this.props.name}</li>;
  }
}
 
export default User;
```

# state and event

Function version

```javascript
import { useState } from 'react';
import User from './User';
 
import classes from './Users.module.css';
 
const DUMMY_USERS = [
  { id: 'u1', name: 'Max' },
  { id: 'u2', name: 'Manuel' },
  { id: 'u3', name: 'Julie' },
];
 
const Users = () => {
  const [showUsers, setShowUsers] = useState(true);
 
  const toggleUsersHandler = () => {
    setShowUsers((curState) => !curState);
  };
 
  const usersList = (
    <ul>
      {DUMMY_USERS.map((user) => (
        <User key={user.id} name={user.name} />
      ))}
    </ul>
  );
 
  return (
    <div className={classes.users}>
      <button onClick={toggleUsersHandler}>
        {showUsers ? 'Hide' : 'Show'} Users
      </button>
      {showUsers && usersList}
    </div>
  );
};
 
export default Users;
```

Class version

```javascript
import { Component } from 'react';
 
import User from './User';
import classes from './Users.module.css';
 
const DUMMY_USERS = [
  { id: 'u1', name: 'Max' },
  { id: 'u2', name: 'Manuel' },
  { id: 'u3', name: 'Julie' },
];
 
class Users extends Component {
  constructor() {
    super();
    this.state = {
      showUsers: true,
      more: 'Test',
    };
  }
 
  toggleUsersHandler() {
    // this.state.showUsers = false; // NOT!
    this.setState((curState) => {
      return { showUsers: !curState.showUsers };
    });
  }
 
  render() {
    const usersList = (
      <ul>
        {DUMMY_USERS.map((user) => (
          <User key={user.id} name={user.name} />
        ))}
      </ul>
    );
 
    return (
      <div className={classes.users}>
        <button onClick={this.toggleUsersHandler.bind(this)}>
          {this.state.showUsers ? 'Hide' : 'Show'} Users
        </button>
        {this.state.showUsers && usersList}
      </div>
    );
  }
}
 
export default Users;
```

# useEffect / side effects

Function version

```javascript
import { Fragment, useState, useEffect } from 'react';
 
import Users from './Users';
import classes from './UserFinder.module.css';
 
const DUMMY_USERS = [
  { id: 'u1', name: 'Max' },
  { id: 'u2', name: 'Manuel' },
  { id: 'u3', name: 'Julie' },
];
 
const UserFinder = () => {
  const [filteredUsers, setFilteredUsers] = useState(DUMMY_USERS);
  const [searchTerm, setSearchTerm] = useState('');
 
  useEffect(() => {
    setFilteredUsers(
      DUMMY_USERS.filter((user) => user.name.includes(searchTerm))
    );
  }, [searchTerm]);
 
  const searchChangeHandler = (event) => {
    setSearchTerm(event.target.value);
  };
 
  return (
    <Fragment>
      <div className={classes.finder}>
        <input type='search' onChange={searchChangeHandler} />
      </div>
      <Users users={filteredUsers} />
    </Fragment>
  );
};
 
export default UserFinder;
```

Class version

```javascript
import { Fragment, useState, useEffect, Component } from 'react';
 
import Users from './Users';
import classes from './UserFinder.module.css';
 
const DUMMY_USERS = [
  { id: 'u1', name: 'Max' },
  { id: 'u2', name: 'Manuel' },
  { id: 'u3', name: 'Julie' },
];
 
class UserFinder extends Component {
  constructor() {
    super();
    this.state = {
      filteredUsers: [],
      searchTerm: '',
    };
  }
 
  componentDidMount() {
    // Send http request...
    this.setState({ filteredUsers: DUMMY_USERS });
  }
 
  componentDidUpdate(prevProps, prevState) {
    if (prevState.searchTerm !== this.state.searchTerm) {
      this.setState({
        filteredUsers: DUMMY_USERS.filter((user) =>
          user.name.includes(this.state.searchTerm)
        ),
      });
    }
  }
 
  searchChangeHandler(event) {
    this.setState({ searchTerm: event.target.value });
  }
 
  render() {
    return (
      <Fragment>
        <div className={classes.finder}>
          <input type='search' onChange={this.searchChangeHandler.bind(this)} />
        </div>
        <Users users={this.state.filteredUsers} />
      </Fragment>
    );
  }
}
 
export default UserFinder;
```

# Context 

https://github.com/fredericreact/classbasedcomponents/commit/2a4d6deaebb98a6330542c4cb7116331e7edf20f 

Provide same as function

```javascript
import React from 'react';
 
const UsersContext = React.createContext({
  users: []
});
 
export default UsersContext;
```

```javascript
import UserFinder from './components/UserFinder';
import UsersContext from './store/users-context';
 
const DUMMY_USERS = [
  { id: 'u1', name: 'Max' },
  { id: 'u2', name: 'Manuel' },
  { id: 'u3', name: 'Julie' },
];
 
function App() {
  const usersContext = {
    users: DUMMY_USERS
  }
 
  return (
    <UsersContext.Provider value={usersContext}>
      <UserFinder />
    </UsersContext.Provider>
  );
}
 
export default App;
```

UseContext different vs function version

```javascript
import { Fragment, useState, useEffect, Component } from 'react';
 
import Users from './Users';
import classes from './UserFinder.module.css';
import UsersContext from '../store/users-context';
 
class UserFinder extends Component {
  static contextType = UsersContext;
 
  constructor() {
    super();
    this.state = {
      filteredUsers: [],
      searchTerm: '',
    };
  }
 
  componentDidMount() {
    // Send http request...
    this.setState({ filteredUsers: this.context.users });
  }
 
  componentDidUpdate(prevProps, prevState) {
    if (prevState.searchTerm !== this.state.searchTerm) {
      this.setState({
        filteredUsers: this.context.users.filter((user) =>
          user.name.includes(this.state.searchTerm)
        ),
      });
    }
  }
 
  searchChangeHandler(event) {
    this.setState({ searchTerm: event.target.value });
  }
 
  render() {
    return (
      <Fragment>
        <div className={classes.finder}>
          <input type='search' onChange={this.searchChangeHandler.bind(this)} />
        </div>
        <Users users={this.state.filteredUsers} />
      </Fragment>
    );
  }
}
 
 
 
export default UserFinder;
```

# Error boundaries

C’est comme try catch dans javascript

Ca permet de handle les erreur proprement. 

Les erreur bouderies ne peux etre gerees que par des classes
