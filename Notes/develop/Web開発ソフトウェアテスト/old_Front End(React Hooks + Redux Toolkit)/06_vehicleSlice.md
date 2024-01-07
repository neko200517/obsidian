## vehicleSliceの作成 

src/features/vehicleSliceファイルを作成して、各種状態を保存する

```bash
mkdir features 
touch features/vehicleSlice.js
```

### 各種APIアクセス用の関数を定義

REST API アクセス用のフェッチ関数を作成

```js
// src/features/vehicleSlice.js

import { createSlice, createAsyncThunk } from '@reduxjs/toolkit';
import axios from 'axios';

const apiUrl = 'http://localhost:8000/';

// Segment GET
export const fetchAsyncGetSegments = createAsyncThunk(
  'segment/get',
  async () => {
    const res = await axios.get(`${apiUrl}api/segments/`, {
      headers: {
        Authorization: `token ${localStorage.token}`,
      },
    });
    return res.data;
  }
);

// Segment Create
export const fetchAsyncCreateSegment = createAsyncThunk(
  'segment/post',
  async (segment) => {
    const res = await axios.post(`${apiUrl}api/segments/`, segment, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `token ${localStorage.token}`,
      },
    });
    return res.data;
  }
);

// Segment Update
export const fetchAsyncUpdateSegment = createAsyncThunk(
  'segment/put',
  async (segment) => {
    const res = await axios.put(
      `${apiUrl}api/segments/${segment.id}`,
      segment,
      {
        headers: {
          'Content-Type': 'application/json',
          Authorization: `token ${localStorage.token}`,
        },
      }
    );
    return res.data;
  }
);

// Segment Delete
export const fetchAsyncDeleteSegment = createAsyncThunk(
  'segment/delete',
  async (id) => {
    await axios.delete(`${apiUrl}api/segments/${id}`, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `token ${localStorage.token}`,
      },
    });
    return id;
  }
);

// Brand GET
export const fetchAsyncGetBrands = createAsyncThunk('brand/get', async () => {
  const res = await axios.get(`${apiUrl}api/brands/`, {
    headers: {
      Authorization: `token ${localStorage.token}`,
    },
  });
  return res.data;
});

// Brand Create
export const fetchAsyncCreateBrand = createAsyncThunk(
  'brand/post',
  async (brand) => {
    const res = await axios.post(`${apiUrl}api/brands/`, brand, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `token ${localStorage.token}`,
      },
    });
    return res.data;
  }
);

// Brand Update
export const fetchAsyncUpdateBrand = createAsyncThunk(
  'brand/put',
  async (brand) => {
    const res = await axios.put(`${apiUrl}api/brands/${brand.id}`, brand, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `token ${localStorage.token}`,
      },
    });
    return res.data;
  }
);

// Brand Delete
export const fetchAsyncDeleteBrand = createAsyncThunk(
  'brand/delete',
  async (id) => {
    await axios.delete(`${apiUrl}api/brands/${id}`, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `token ${localStorage.token}`,
      },
    });
    return id;
  }
);

// Vehicle GET
export const fetchAsyncGetVehicles = createAsyncThunk(
  'vehicle/get',
  async () => {
    const res = await axios.get(`${apiUrl}api/vehicles/`, {
      headers: {
        Authorization: `token ${localStorage.token}`,
      },
    });
    return res.data;
  }
);

// Vehicle Create
export const fetchAsyncCreateVehicle = createAsyncThunk(
  'vehicle/post',
  async (vehicle) => {
    const res = await axios.post(`${apiUrl}api/vehicles/`, vehicle, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `token ${localStorage.token}`,
      },
    });
    return res.data;
  }
);

// Vehicle Update
export const fetchAsyncUpdateVehicle = createAsyncThunk(
  'vehicle/put',
  async (vehicle) => {
    const res = await axios.put(
      `${apiUrl}api/vehicles/${vehicle.id}`,
      vehicle,
      {
        headers: {
          'Content-Type': 'application/json',
          Authorization: `token ${localStorage.token}`,
        },
      }
    );
    return res.data;
  }
);

// Vehicle Delete
export const fetchAsyncDeleteVehicle = createAsyncThunk(
  'vehicle/delete',
  async (id) => {
    await axios.delete(`${apiUrl}api/vehicles/${id}`, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `token ${localStorage.token}`,
      },
    });
    return id;
  }
);
```

### vehicleSliceの作成

ReduxToolkitで各種状態管理、Reducer、後処理の定義

```js
// src/features/vehicleSlice.js

// 中略

// vehicleSliceの作成
export const vehicleSlice = createSlice({
  name: 'vehicle',
  initialState: {
    segments: [
      {
        id: 0,
        segment_name: '',
      },
    ],
    brands: [
      {
        id: 0,
        brand_name: '',
      },
    ],
    vehicles: [
      {
        id: 0,
        vehicle_name: '',
        release_year: 2020,
        price: 0.0,
        segment: 0,
        brand: 0,
        segment_name: '',
        brand_name: '',
      },
    ],
    // 編集用
    editedSegment: {
      id: 0,
      segment_name: '',
    },
    editedBrand: {
      id: 0,
      brand_name: '',
    },
    editedVehicle: {
      id: 0,
      vehicle_name: '',
      release_year: 2020,
      price: 0.0,
      segment: 0,
      brand: 0,
    },
  },
  // Reducers
  reducers: {
    // 編集した内容をeditedSegmentに格納
    editSegment(state, action) {
      state.editedSegment = action.payload;
    },
    // 編集した内容をeditedBrandに格納
    editBrand(state, action) {
      state.editedBrand = action.payload;
    },
    // 編集した内容をeditedVehicleに格納
    editVehicle(state, action) {
      state.editedVehicle = action.payload;
    },
  },
  // 後処理
  extraReducers: (builder) => {
    // Get Segments API を呼んだ場合
    builder.addCase(fetchAsyncGetSegments.fulfilled, (state, action) => {
      return {
        ...state,
        segments: action.payload,
      };
    });
    // Create Segment API を呼んだ場合
    builder.addCase(fetchAsyncCreateSegment.fulfilled, (state, action) => {
      return {
        ...state,
        segments: [...state.segments, action.payload],
      };
    });
    // Update Segment API を呼んだ場合
    builder.addCase(fetchAsyncUpdateSegment.fulfilled, (state, action) => {
      return {
        ...state,
        segments: state.segments.map((seg) =>
          seg.id === action.payload.id ? action.payload : seg
        ),
        // Vehicleの更新
        vehicles: state.vehicles.map((veh) =>
          veh.segment === action.payload.id
            ? { ...veh, segment_name: action.payload.segment_name }
            : veh
        ),
      };
    });
    // Delete Segment API を呼んだ場合
    builder.addCase(fetchAsyncDeleteSegment.fulfilled, (state, action) => {
      return {
        ...state,
        segments: state.segments.filter((seg) => seg.id !== action.payload),
        vehicles: state.vehicles.filter(
          (veh) => veh.segment !== action.payload
        ),
      };
    });
    // Get Brands API を呼んだ場合
    builder.addCase(fetchAsyncGetBrands.fulfilled, (state, action) => {
      return {
        ...state,
        brands: action.payload,
      };
    });
    // Create Brand API を呼んだ場合
    builder.addCase(fetchAsyncCreateBrand.fulfilled, (state, action) => {
      return {
        ...state,
        brands: [...state.brands, action.payload],
      };
    });
    // Update Brand API を呼んだ場合
    builder.addCase(fetchAsyncUpdateBrand.fulfilled, (state, action) => {
      return {
        ...state,
        brands: state.brands.map((seg) =>
          seg.id === action.payload.id ? action.payload : seg
        ),
        // Vehicleの更新
        vehicles: state.vehicles.map((veh) =>
          veh.brand === action.payload.id
            ? { ...veh, brand_name: action.payload.brand_name }
            : veh
        ),
      };
    });
    // Delete Brand API を呼んだ場合
    builder.addCase(fetchAsyncDeleteBrand.fulfilled, (state, action) => {
      return {
        ...state,
        brands: state.brands.filter((seg) => seg.id !== action.payload),
        vehicles: state.vehicles.filter((veh) => veh.brand !== action.payload),
      };
    });
    // Get Vehicle API を呼んだ場合
    builder.addCase(fetchAsyncGetVehicles.fulfilled, (state, action) => {
      return {
        ...state,
        vehicles: action.payload,
      };
    });
    // Create Vehicle API を呼んだ場合
    builder.addCase(fetchAsyncCreateVehicle.fulfilled, (state, action) => {
      return {
        ...state,
        vehicles: [...state.vehicles, action.payload],
      };
    });
    // Update Vehicle API を呼んだ場合
    builder.addCase(fetchAsyncUpdateVehicle.fulfilled, (state, action) => {
      return {
        ...state,
        vehicles: state.vehicles.map((seg) =>
          seg.id === action.payload.id ? action.payload : seg
        ),
      };
    });
    // Delete Vehicle API を呼んだ場合
    builder.addCase(fetchAsyncDeleteVehicle.fulfilled, (state, action) => {
      return {
        ...state,
        vehicles: state.vehicles.filter(
          (vehicle) => vehicle.id !== action.payload
        ),
      };
    });
  },
});

// 外部から呼び出し可能なアクション
export const { editSegment, editBrand, editVehicle } = vehicleSlice.actions;

// 各種ステートを取得
export const selectSegments = (state) => state.vehicle.segments;
export const selectEditedSegment = (state) => state.vehicle.editedSegment;
export const selectBrands = (state) => state.vehicle.brands;
export const selectEditedBrand = (state) => state.vehicle.editedBrand;
export const selectVehicles = (state) => state.vehicle.vehicles;
export const selectEditedVehicle = (state) => state.vehicle.editedVehicle;

export default vehicleSlice.reducer;
```

## Reducerの登録

作成したReducerを登録する

```js
// src/app/store.js

import { configureStore } from '@reduxjs/toolkit';
import authReducer from '../features/authSlice';
import vehicleReducer from '../features/vehicleSlice';

export const store = configureStore({
  reducer: {
    auth: authReducer,
    vehicle: vehicleReducer,
  },
});
```
