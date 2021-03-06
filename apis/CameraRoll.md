All rights reserved.

This source code is licensed under the BSD-style license found in the LICENSE file in the root directory of this source tree. An additional grant of patent rights can be found in the PATENTS file in the same directory.

@flow

## Methods 

static **saveImageWithTag**(tag: string, successCallback, errorCallback) 

Saves the image with tag `tag` to the camera roll.

@param {string} tag - Can be any of the three kinds of tags we accept: 1. URL 2. assets-library tag 3. tag returned from storing an image in memory

static **getPhotos**(params: object, callback: function, errorCallback: function) 

Invokes `callback` with photo identifier objects from the local camera roll of the device matching shape defined by `getPhotosReturnChecker`.

@param {object} params - See `getPhotosParamChecker`. @param {function} callback - Invoked with arg of shape defined by `getPhotosReturnChecker` on success. @param {function} errorCallback - Invoked with error message on error.

## Examples 

```javascript
'use strict';

var React = require('react-native');
var {
  CameraRoll,
  Image,
  SliderIOS,
  StyleSheet,
  SwitchIOS,
  Text,
  View,
} = React;

var CameraRollView = require('./CameraRollView.ios');

var CAMERA_ROLL_VIEW = 'camera_roll_view';

var CameraRollExample = React.createClass({

  getInitialState() {
    return {
      groupTypes: 'SavedPhotos',
      sliderValue: 1,
      bigImages: true,
    };
  },

  render() {
    return (
      <View>
        <SwitchIOS
          onValueChange={this._onSwitchChange}
          value={this.state.bigImages} />
        <Text>{(this.state.bigImages ? 'Big' : 'Small') + ' Images'}</Text>
        <SliderIOS
          value={this.state.sliderValue}
          onValueChange={this._onSliderChange}
        />
        <Text>{'Group Type: ' + this.state.groupTypes}</Text>
        <CameraRollView
          ref={CAMERA_ROLL_VIEW}
          batchSize={5}
          groupTypes={this.state.groupTypes}
          renderImage={this._renderImage}
        />
      </View>
    );
  },

  _renderImage(asset) {
    var imageSize = this.state.bigImages ? 150 : 75;
    var imageStyle = [styles.image, {width: imageSize, height: imageSize}];
    var location = asset.node.location.longitude ?
      JSON.stringify(asset.node.location) : 'Unknown location';
    return (
      <View key={asset} style={styles.row}>
        <Image
          source={asset.node.image}
          style={imageStyle}
        />
        <View style={styles.info}>
          <Text style={styles.url}>{asset.node.image.uri}</Text>
          <Text>{location}</Text>
          <Text>{asset.node.group_name}</Text>
          <Text>{new Date(asset.node.timestamp).toString()}</Text>
        </View>
      </View>
    );
  },

  _onSliderChange(value) {
    var options = CameraRoll.GroupTypesOptions;
    var index = Math.floor(value * options.length * 0.99);
    var groupTypes = options[index];
    if (groupTypes !== this.state.groupTypes) {
      this.setState({groupTypes: groupTypes});
    }
  },

  _onSwitchChange(value) {
    this.refs[CAMERA_ROLL_VIEW].rendererChanged();
    this.setState({ bigImages: value });
  }
});

var styles = StyleSheet.create({
  row: {
    flexDirection: 'row',
    flex: 1,
  },
  url: {
    fontSize: 9,
    marginBottom: 14,
  },
  image: {
    margin: 4,
  },
  info: {
    flex: 1,
  },
});

exports.title = '<CameraRollView>';
exports.description = 'Example component that uses CameraRoll to list user\'s photos';
exports.examples = [
  {
    title: 'Photos',
    render(): ReactElement { return <CameraRollExample />; }
  }
];
```