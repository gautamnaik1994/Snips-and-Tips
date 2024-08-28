# Media Queries

### Ipad Pro Media queries

```scss
@mixin ipadPro {
  @media only screen and (min-width: 1024px) and (max-height: 1366px) and (orientation: portrait) and (-webkit-min-device-pixel-ratio: 1.5) {
    @content;
  }
}

@mixin ipadProLandscape {
  @media only screen and (min-width: 1024px) and (max-height: 1366px) and (orientation: landscape) and (-webkit-min-device-pixel-ratio: 1.5) and (hover: none) {
    @content;
  }
}
```

### Ipad Media queries

```scss
@mixin ipad {
  @media all and (device-width: 768px) and (device-height: 1024px) and (orientation: portrait) and (-webkit-min-device-pixel-ratio: 1.5) and (hover: none) {
    @content;
  }
}
@mixin ipadLandscape {
  @media all and (device-width: 1024px) and (device-height: 768px) and (orientation: landscape) and (-webkit-min-device-pixel-ratio: 1.5) and (hover: none) {
    @content;
  }
}
```

### Usage

```scss
@include ipad {
  font-size: 25px;
}

@include ipadPro {
  font-size: 25px;
}
```
