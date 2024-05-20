---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - vite
  - typescript
  - react-hooks
  - redux-toolkit
  - test
aliases:
  - <% tp.file.title %>
---

## vehicleSlice の作成

src/features/vehicleSlice ファイルを作成して、各種状態を保存する

```bash
mkdir features
touch features/vehicleSlice.ts
```

### src/features/vehicleSlice.ts

REST API アクセス用のフェッチ関数を作成

```ts
// src/features/vehicleSlice.ts

import { createSlice, createAsyncThunk, PayloadAction } from '@reduxjs/toolkit';
import axios from 'axios';

const apiUrl = 'http://localhost:8000/';

export interface SegmentModel {
  id?: number;
  segment_name: string;
}

export interface EditedSegmentModel {
  id?: number;
  segment_name: string;
}

export interface BrandModel {
  id?: number;
  brand_name: string;
}

export interface EditedBrandModel {
  id?: number;
  brand_name: string;
}

export interface VehicleModel {
  id?: number;
  vehicle_name: string;
  release_year: number;
  price: number;
  segment: number;
  brand: number;
  segment_name: string;
  brand_name: string;
}

export interface EditedVehicleModel {
  id?: number;
  vehicle_name: string;
  release_year: number;
  price: number;
  segment: number;
  brand: number;
}

interface SegmentState {
  segments: SegmentModel[];
}

interface EditedSegmentState {
  editedSegment: SegmentModel;
}

interface BrandState {
  brands: BrandModel[];
}

interface EditedBrandState {
  editedBrand: BrandModel;
}

interface VehicleState {
  vehicles: VehicleModel[];
}

interface EditedVehicleState {
  editedVehicle: EditedVehicleModel;
}

const initialState: {
  segments: SegmentModel[];
  brands: BrandModel[];
  vehicles: VehicleModel[];
  editedSegment: EditedSegmentModel;
  editedBrand: EditedBrandModel;
  editedVehicle: EditedVehicleModel;
} = {
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
};

// SegmentModel GET
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

// SegmentModel Create
export const fetchAsyncCreateSegment = createAsyncThunk(
  'segment/post',
  async (segment: EditedSegmentModel) => {
    const res = await axios.post(`${apiUrl}api/segments/`, segment, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `token ${localStorage.token}`,
      },
    });
    return res.data;
  }
);

// SegmentModel Update
export const fetchAsyncUpdateSegment = createAsyncThunk(
  'segment/put',
  async (segment: EditedSegmentModel) => {
    const res = await axios.put(
      `${apiUrl}api/segments/${segment.id}/`,
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

// SegmentModel Delete
export const fetchAsyncDeleteSegment = createAsyncThunk(
  'segment/delete',
  async (id: number) => {
    await axios.delete(`${apiUrl}api/segments/${id}/`, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `token ${localStorage.token}`,
      },
    });
    return id;
  }
);

// BrandModel GET
export const fetchAsyncGetBrands = createAsyncThunk('brand/get', async () => {
  const res = await axios.get(`${apiUrl}api/brands/`, {
    headers: {
      Authorization: `token ${localStorage.token}`,
    },
  });
  return res.data;
});

// BrandModel Create
export const fetchAsyncCreateBrand = createAsyncThunk(
  'brand/post',
  async (brand: EditedBrandModel) => {
    const res = await axios.post(`${apiUrl}api/brands/`, brand, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `token ${localStorage.token}`,
      },
    });
    return res.data;
  }
);

// BrandModel Update
export const fetchAsyncUpdateBrand = createAsyncThunk(
  'brand/put',
  async (brand: EditedBrandModel) => {
    const res = await axios.put(`${apiUrl}api/brands/${brand.id}/`, brand, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `token ${localStorage.token}`,
      },
    });
    return res.data;
  }
);

// BrandModel Delete
export const fetchAsyncDeleteBrand = createAsyncThunk(
  'brand/delete',
  async (id: number) => {
    await axios.delete(`${apiUrl}api/brands/${id}/`, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `token ${localStorage.token}`,
      },
    });
    return id;
  }
);

// VehicleModel GET
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

// VehicleModel Create
export const fetchAsyncCreateVehicle = createAsyncThunk(
  'vehicle/post',
  async (vehicle: EditedVehicleModel) => {
    const res = await axios.post(`${apiUrl}api/vehicles/`, vehicle, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `token ${localStorage.token}`,
      },
    });
    return res.data;
  }
);

// VehicleModel Update
export const fetchAsyncUpdateVehicle = createAsyncThunk(
  'vehicle/put',
  async (vehicle: EditedVehicleModel) => {
    const res = await axios.put(
      `${apiUrl}api/vehicles/${vehicle.id}/`,
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

// VehicleModel Delete
export const fetchAsyncDeleteVehicle = createAsyncThunk(
  'vehicle/delete',
  async (id: number) => {
    await axios.delete(`${apiUrl}api/vehicles/${id}/`, {
      headers: {
        'Content-Type': 'application/json',
        Authorization: `token ${localStorage.token}`,
      },
    });
    return id;
  }
);

// vehicleSliceの作成
export const vehicleSlice = createSlice({
  name: 'vehicle',
  initialState,
  // Reducers
  reducers: {
    // 編集した内容をeditedSegmentに格納
    editSegment(
      state: typeof initialState,
      action: PayloadAction<EditedSegmentModel>
    ) {
      state.editedSegment = action.payload;
    },
    // 編集した内容をeditedBrandに格納
    editBrand(
      state: typeof initialState,
      action: PayloadAction<EditedBrandModel>
    ) {
      state.editedBrand = action.payload;
    },
    // 編集した内容をeditedVehicleに格納
    editVehicle(
      state: typeof initialState,
      action: PayloadAction<EditedVehicleModel>
    ) {
      state.editedVehicle = action.payload;
    },
  },
  // 後処理
  extraReducers: (builder) => {
    // Get Segments API を呼んだ場合
    builder.addCase(
      fetchAsyncGetSegments.fulfilled,
      (state: typeof initialState, action: PayloadAction<SegmentModel[]>) => {
        return {
          ...state,
          segments: action.payload,
        };
      }
    );
    // Create SegmentModel API を呼んだ場合
    builder.addCase(
      fetchAsyncCreateSegment.fulfilled,
      (state: typeof initialState, action: PayloadAction<SegmentModel>) => {
        return {
          ...state,
          segments: [...state.segments, action.payload],
        };
      }
    );
    // Update SegmentModel API を呼んだ場合
    builder.addCase(
      fetchAsyncUpdateSegment.fulfilled,
      (state: typeof initialState, action: PayloadAction<SegmentModel>) => {
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
      }
    );
    // Delete SegmentModel API を呼んだ場合
    builder.addCase(
      fetchAsyncDeleteSegment.fulfilled,
      (state: typeof initialState, action: PayloadAction<number>) => {
        return {
          ...state,
          segments: state.segments.filter((seg) => seg.id !== action.payload),
          vehicles: state.vehicles.filter(
            (veh) => veh.segment !== action.payload
          ),
        };
      }
    );
    // Get Brands API を呼んだ場合
    builder.addCase(
      fetchAsyncGetBrands.fulfilled,
      (state: typeof initialState, action: PayloadAction<BrandModel[]>) => {
        return {
          ...state,
          brands: action.payload,
        };
      }
    );
    // Create BrandModel API を呼んだ場合
    builder.addCase(
      fetchAsyncCreateBrand.fulfilled,
      (state: typeof initialState, action: PayloadAction<BrandModel>) => {
        return {
          ...state,
          brands: [...state.brands, action.payload],
        };
      }
    );
    // Update BrandModel API を呼んだ場合
    builder.addCase(
      fetchAsyncUpdateBrand.fulfilled,
      (state: typeof initialState, action: PayloadAction<BrandModel>) => {
        return {
          ...state,
          brands: state.brands.map((brand) =>
            brand.id === action.payload.id ? action.payload : brand
          ),
          // Vehicleの更新
          vehicles: state.vehicles.map((veh) =>
            veh.brand === action.payload.id
              ? { ...veh, brand_name: action.payload.brand_name }
              : veh
          ),
        };
      }
    );
    // Delete BrandModel API を呼んだ場合
    builder.addCase(
      fetchAsyncDeleteBrand.fulfilled,
      (state: typeof initialState, action: PayloadAction<number>) => {
        return {
          ...state,
          brands: state.brands.filter((brand) => brand.id !== action.payload),
          vehicles: state.vehicles.filter(
            (veh) => veh.brand !== action.payload
          ),
        };
      }
    );
    // Get VehicleModel API を呼んだ場合
    builder.addCase(
      fetchAsyncGetVehicles.fulfilled,
      (state: typeof initialState, action: PayloadAction<VehicleModel[]>) => {
        return {
          ...state,
          vehicles: action.payload,
        };
      }
    );
    // Create VehicleModel API を呼んだ場合
    builder.addCase(
      fetchAsyncCreateVehicle.fulfilled,
      (state: typeof initialState, action: PayloadAction<VehicleModel>) => {
        return {
          ...state,
          vehicles: [...state.vehicles, action.payload],
        };
      }
    );
    // Update VehicleModel API を呼んだ場合
    builder.addCase(
      fetchAsyncUpdateVehicle.fulfilled,
      (state: typeof initialState, action: PayloadAction<VehicleModel>) => {
        return {
          ...state,
          vehicles: state.vehicles.map((veh) =>
            veh.id === action.payload.id ? action.payload : veh
          ),
        };
      }
    );
    // Delete VehicleModel API を呼んだ場合
    builder.addCase(
      fetchAsyncDeleteVehicle.fulfilled,
      (state: typeof initialState, action: PayloadAction<number>) => {
        return {
          ...state,
          vehicles: state.vehicles.filter((veh) => veh.id !== action.payload),
        };
      }
    );
  },
});

// 外部から呼び出し可能なアクション
export const { editSegment, editBrand, editVehicle } = vehicleSlice.actions;

// 各種ステートを取得
export const selectSegments = (state: { vehicle: SegmentState }) =>
  state.vehicle.segments;
export const selectEditedSegment = (state: { vehicle: EditedSegmentState }) =>
  state.vehicle.editedSegment;
export const selectBrands = (state: { vehicle: BrandState }) =>
  state.vehicle.brands;
export const selectEditedBrand = (state: { vehicle: EditedBrandState }) =>
  state.vehicle.editedBrand;
export const selectVehicles = (state: { vehicle: VehicleState }) =>
  state.vehicle.vehicles;
export const selectEditedVehicle = (state: { vehicle: EditedVehicleState }) =>
  state.vehicle.editedVehicle;

export default vehicleSlice.reducer;
```

## Reducer の登録

作成した Reducer を登録する

```ts
// src/store.ts

import { configureStore, ThunkAction, Action } from '@reduxjs/toolkit';
import authReducer from '../features/authSlice';
import vehicleReducer from '../features/vehicleSlice';

export const store = configureStore({
  reducer: {
    auth: authReducer,
    vehicle: vehicleReducer,
  },
});

export type AppDispatch = typeof store.dispatch;
export type RootState = ReturnType<typeof store.getState>;
export type AppThunk<ReturnType = void> = ThunkAction<
  ReturnType,
  RootState,
  unknown,
  Action<string>
>;
```
