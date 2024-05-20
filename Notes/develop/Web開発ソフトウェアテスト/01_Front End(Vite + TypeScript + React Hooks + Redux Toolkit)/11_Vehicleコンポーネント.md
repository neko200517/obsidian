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

## Vehicle コンポーネントの作成

Vehicle の新規登録、更新、削除画面

```ts
// src/components/Vehicle.ts

import { useState, useEffect, useRef } from 'react';
import styles from './Vehicle.module.css';
import { useAppDispatch, useAppSelector } from '../app/hooks';
import {
  fetchAsyncGetVehicles,
  fetchAsyncCreateVehicle,
  fetchAsyncUpdateVehicle,
  fetchAsyncDeleteVehicle,
  editVehicle,
  selectSegments,
  selectBrands,
  selectVehicles,
  selectEditedVehicle,
  EditedVehicleModel,
} from '../features/vehicleSlice';

const YEN_RATE = 142.28;

const Vehicle = () => {
  const dispatch = useAppDispatch();
  const segments = useAppSelector(selectSegments);
  const brands = useAppSelector(selectBrands);
  const vehicles = useAppSelector(selectVehicles);
  const editedVehicle = useAppSelector(selectEditedVehicle);
  const [successMsg, setSuccessMsg] = useState('');
  const inputRef = useRef<HTMLInputElement>(null);

  const segmentOptions = segments?.map((seg) => (
    <option key={seg.id} value={seg.id}>
      {seg.segment_name}
    </option>
  ));

  const brandOptions = brands?.map((brand) => (
    <option key={brand.id} value={brand.id}>
      {brand.brand_name}
    </option>
  ));

  useEffect(() => {
    const fetchBootLoader = async () => {
      const result = await dispatch(fetchAsyncGetVehicles());
      if (fetchAsyncGetVehicles.rejected.match(result)) {
        setSuccessMsg('Get error!');
      }
    };
    fetchBootLoader();
  }, [dispatch]);

  // Createボタン
  const onCreate = async () => {
    await dispatch(fetchAsyncCreateVehicle(editedVehicle));
    await clearEditedVehicle();
  };

  // Updateボタン
  const onUpdate = async () => {
    const result = await dispatch(fetchAsyncUpdateVehicle(editedVehicle));
    if (fetchAsyncUpdateVehicle.fulfilled.match(result)) {
      setSuccessMsg('Updated in vehicle!');
    }
    await clearEditedVehicle();
  };

  // Editボタン
  const onFocusInput = async (vehicle: EditedVehicleModel) => {
    await dispatch(editVehicle(vehicle));
    inputRef.current?.focus();
  };

  // Deleteボタン
  const onDelete = async (id: number) => {
    const result = await dispatch(fetchAsyncDeleteVehicle(id));
    if (fetchAsyncDeleteVehicle.fulfilled.match(result)) {
      setSuccessMsg('Deleted in vehicle!');
    }
    await clearEditedVehicle();
  };

  // editedVehicleを初期化
  const clearEditedVehicle = async () => {
    await dispatch(
      editVehicle({
        id: 0,
        vehicle_name: '',
        release_year: 2020,
        price: 0.0,
        segment: 0,
        brand: 0,
      })
    );
  };

  return (
    <>
      <h3 data-testid='h3-vehicle'>Vehicle</h3>
      <span className={styles.vehicle__status}>{successMsg}</span>

      <div className={styles.vehicle__input}>
        {/* 名前 */}
        <input
          type='text'
          placeholder='new vehicle name'
          value={editedVehicle.vehicle_name}
          ref={inputRef}
          onChange={async (e) =>
            await dispatch(
              editVehicle({ ...editedVehicle, vehicle_name: e.target.value })
            )
          }
        />

        {/* リリース年 */}
        <input
          type='number'
          placeholder='year of release'
          min='0'
          value={editedVehicle.release_year}
          onChange={async (e) =>
            await dispatch(
              editVehicle({
                ...editedVehicle,
                release_year: Number(e.target.value),
              })
            )
          }
        />

        {/* 価格 */}
        <input
          type='number'
          placeholder='price'
          min='0'
          step='0.01'
          value={editedVehicle.price}
          onChange={async (e) =>
            await dispatch(
              editVehicle({ ...editedVehicle, price: Number(e.target.value) })
            )
          }
        />
      </div>

      <div>
        {/* Select Segment */}
        <select
          data-testid='select-segment'
          value={editedVehicle.segment}
          onChange={async (e) => {
            await dispatch(
              editVehicle({ ...editedVehicle, segment: Number(e.target.value) })
            );
          }}
        >
          <option value={0}>Segment</option>
          {segmentOptions}
        </select>

        {/* Select Brand */}
        <select
          data-testid='select-brand'
          value={editedVehicle.brand}
          onChange={async (e) => {
            await dispatch(
              editVehicle({ ...editedVehicle, brand: Number(e.target.value) })
            );
          }}
        >
          <option value={0}>Brand</option>
          {brandOptions}
        </select>

        {/* 登録ボタン */}
        <button
          data-testid='btn-vehicle-post'
          onClick={editedVehicle.id === 0 ? onCreate : onUpdate}
          disabled={
            !(
              editedVehicle.vehicle_name !== '' &&
              editedVehicle.segment > 0 &&
              editedVehicle.brand > 0
            )
          }
        >
          {editedVehicle.id === 0 ? 'Create' : 'Update'}
        </button>
      </div>

      {/* 一覧 */}
      <div>
        <ul>
          {vehicles?.map((vehicle) => (
            <li key={vehicle.id} className={styles.vehicle__item}>
              <span data-testid={`list-${vehicle.id}`}>
                <strong data-testid={`name-${vehicle.id}`}>
                  {vehicle.vehicle_name}
                </strong>
                ---{vehicle.release_year} ---￥
                {(vehicle.price * YEN_RATE).toFixed(2)}
                [M] ---
                {vehicle.segment_name} {vehicle.brand_name}---
              </span>
              <div>
                {/* 更新ボタン */}
                <button
                  data-testid={`edit-veh-${vehicle.id}`}
                  onClick={() => onFocusInput(vehicle)}
                >
                  Edit
                </button>
                {/* 削除ボタン */}
                <button
                  data-testid={`delete-veh-${vehicle.id}`}
                  onClick={() => onDelete(vehicle.id!)}
                >
                  Delete
                </button>
              </div>
            </li>
          ))}
        </ul>
      </div>
    </>
  );
};

export default Vehicle;
```
