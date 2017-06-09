# Actors In Movies - React Native Demo App

![Rithm School Logo](./rithm-school-company-logo.png)

## Setup

Make sure you follow the [react native getting started](https://facebook.github.io/react-native/docs/getting-started.html) guide from facebook.  You will want to follow the `Building Projects With Native Code` tab.

Next, in your terminal, run:

```sh
npm install
npm run start -- --reset-cache
```

In an new tab, run:

```sh
react-native run-ios
```

## Repl.it and expo

You can also use repl.it and expo with this app if you copy the contents of `src/Movies.js` and `src/MovieListItem.js into a new repl.it at [https://repl.it/languages/react_native](https://repl.it/languages/react_native).  Here is the code that you can copy:

```js
import React, {Component} from 'react';
import {
  View,
  Text,
  StyleSheet,
  Image,
  TextInput,
  ScrollView,
  TouchableOpacity
} from 'react-native';
import { Constants } from 'expo';

class MovieListItem extends Component {
  render() {

    // ES2015 object deconstruction
    // Same as const title = this.props.title;
    // Same as const year = this.props.year;
    // Same as const post = this.props.poster;
    const {title, year, poster} = this.props;
    return (
      <View style={{flexDirection: 'row'}}>
        <Image
          style={{width: 100, height: 100}}
          source={{uri: poster}}
        />
        <View style={styles.movieTextView}>
          <Text
            ellipsizeMode="tail"
            numberOfLines={2}
            style={{fontSize:30}}
          >{title}</Text>
          <Text style={{fontSize:20}}>{year}</Text>
        </View>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  movieTextView: {
    flexDirection: 'column',
    flex: 1,
    marginLeft: 4,
    marginRight: 1
  }
});


export default class Movies extends Component {

  constructor(props) {
    super(props);
    this.state = {
      search: '',
      movies: []
    };

    this.getMovies = this.getMovies.bind(this);
  }

  getMovies() {
    const search = encodeURIComponent(this.state.search);
    fetch(`https://netflixroulette.net/api/api.php?actor=${search}`)
      .then(d => d.json())
      .then(d => {
        let movies = [];
        debugger;
        if (d && Array.isArray(d)) {
          movies = d.map(m => {
            return {
              id: m.show_id,
              title: m.show_title,
              year: m.release_year,
              poster: m.poster.replace(/http:\/\//,"https://")
            }
          })
        }
        this.setState({movies});
      })
      .catch(e => console.warn(e));
  }

  render() {
    const movies = this.state.movies.map(m => (
      <MovieListItem
        key={m.id}
        title={m.title}
        year={m.year}
        poster={m.poster}
      />
    ));
    return (
      <View style={movieStyles.container}>
        <View style={{flex: 0.2,
                      flexDirection: 'row',
                      width: "100%",
                      alignItems: 'flex-end',
                      justifyContent: 'space-around',
                      padding: 5,
                    }}>
            <TextInput
                style={{height: 55, alignSelf: 'flex-end', width: "70%", borderColor: 'grey', borderWidth: 1, padding: 5, fontSize: 20}}
                value={this.state.search}
                onChangeText={(search) => this.setState({search})}
             />
            <TouchableOpacity
                style={[movieStyles.buttonContainer]}
                onPress={this.getMovies}
              >
              <Text style={movieStyles.buttonText}>FIND</Text>
            </TouchableOpacity>
        </View>
        <ScrollView style={{flex:0.8, width: "95%"}}>
          {movies}
        </ScrollView>
      </View>
    );
  }
}

const movieStyles = StyleSheet.create({
  container: {
    flex: 1,
    justifyContent: 'center',
    alignItems: 'center',
    backgroundColor: '#F5FCFF',
  },
  buttonContainer: {
    borderRadius: 4,
    backgroundColor: "blue",
    padding:8,
    margin: 2,
  },
  buttonText: {
    color: 'white',
    fontSize: 25,
    textAlign: 'center'
  }
});
```