# 아이템 29. 사용할 때는 너그럽게, 생성할 때는 엄격하게

## tl;dr : 매개변수 타입은 유니온 타입으로, 반환 타입은 명확한 타입으로

함수를 작성할 때 return 타입을 준다면 옵셔널한 타입이 아닌 명확한 타입을 주는 것이 좋다! 일단 책의 예시를 보자. 아래의 긴 예시에서 주목할 점은 CameraOptions 타입과 해당 타입 형태로 값을 반환하는 viewportForBounds함수이다.

```ts
type LngLat =
  | { lng: number; lat: number }
  | { lon: number; lat: number }
  | [number, number];

interface CameraOptions {
  center?: LngLat;
  zoom?: number;
  bearing?: number;
  pitch?: number;
}

type LngLatBounds =
  | { northeast: LngLat; southwest: LngLat }
  | [LngLat, LngLat]
  | [number, number, number, number];

type Feature = {
  geometry: {
    type: string;
    coordinate: [number, number];
  };
};

type BoundingBox = [number, number, number, number];

declare function setCamera(camera: CameraOptions): void;
declare function viewportForBounds(bounds: LngLatBounds): CameraOptions;

function calculateBoundingBox(f: Feature): BoundingBox | null {
  let box: BoundingBox | null = null;

  const helper = (coords: any[]) => {};

  const { geometry } = f;
  if (geometry) {
    helper(geometry.coordinate);
  }

  return box;
}
```

viewportForBounds 함수는 return type이 CameraOptions기 때문에 해당 함수를 통해 받는 값은 지정된 프로퍼티가 모두 있을 수도 있고 아예 비어있을 수도 있다. 따라서 아래와 같이 focusOnFeature 함수를 작성하면 디스트럭처링 된 lat, lng, zoom은 undefined | number 라는 타입을 갖게 되어 이후 타입 가드를 한 번 더 쓰거나 하는 등의 수고가 필요해진다. 사용자의 입장에서 불편!!!

```ts
function focusOnFeature(f: Feature) {
  const bounds = calculateBoundingBox(f);
  const camera = viewportForBounds(bounds!);
  setCamera(camera);
  const {
    center: { lat, lng }, // ~ center는 LngLat | undefined 타입이기 때문에 lat, lng가 없을 수도 있음!
    zoom, // number | undefined
  } = camera;
  zoom;
  window.location.search = `?v=@${lat},${lng}z${zoom}`;
}
```

이렇게 사용되지 않기 위해 아래와 같이 명확한 타입의 Camera 타입을 지정하고, 기존의 함수들에 영향을 주지 않기 위해 CameraOptions 타입도 재정의하고 나면 위와 같이 디스트럭처링하여 사용할 때 문제가 생기지 않는다.

```ts
interface LngLat {
  lng: number;
  lat: number;
}

interface Camera {
  center: LngLat;
  zoom: number;
  bearing: number;
  pitch: number;
}

interface CameraOptions extends Omit<Partial<Camera>, "center"> {
  center?: LngLat;
}

type LngLatBounds =
  | { northeast: LngLatLike; southwest: LngLatLike }
  | [LngLatLike, LngLatLike]
  | [number, number, number, number];
```
