---
date: <% tp.date.now("YYYY-MM-DD") %>
tags:
  - javascript
  - react-hooks
  - redux-toolkit
  - test
  - old
aliases:
  - <% tp.file.title %>
---

## Segment コンポーネントの作成

Segment の新規登録、更新、削除画面

```js
// src/components/Segment.js

import React, { useState, useEffect, useRef } from 'react';
import styles from './Segment.module.css';
import { useDispatch, useSelector } from 'react-redux';
import {
  fetchAsyncGetSegments,
  fetchAsyncCreateSegment,
  fetchAsyncUpdateSegment,
  fetchAsyncDeleteSegment,
  editSegment,
  selectSegments,
  selectEditedSegment,
} from '../features/vehicleSlice';

const Segment = () => {
  const dispatch = useDispatch();
  const segments = useSelector(selectSegments);
  const editedSegment = useSelector(selectEditedSegment);
  const [successMsg, setSuccessMsg] = useState('');
  const inputRef = useRef(null);

  useEffect(() => {
    // 初期化処理
    const fetchBootLoader = async () => {
      const result = await dispatch(fetchAsyncGetSegments());
      // 取得を失敗した場合
      if (fetchAsyncGetSegments.rejected.match(result)) {
        setSuccessMsg('Get error!');
      }
    };
    fetchBootLoader();
  }, [dispatch]);

  // Createボタン
  const onCreate = async () => {
    // Segmentを登録
    await dispatch(
      fetchAsyncCreateSegment({
        segment_name: editedSegment.segment_name,
      })
    );
    await clearEditedValue();
  };

  // Updateボタン
  const onUpdate = async () => {
    const result = await dispatch(fetchAsyncUpdateSegment(editedSegment));
    if (fetchAsyncUpdateSegment.fulfilled.match(result)) {
      setSuccessMsg('Updated in segment!');
    }
    await clearEditedValue();
  };

  // Editボタン
  const onFocusInput = async (segment) => {
    await dispatch(editSegment(segment));
    inputRef.current?.focus();
  };

  // Deleteボタン
  const onDelete = async (id) => {
    const result = await dispatch(fetchAsyncDeleteSegment(id));
    if (fetchAsyncDeleteSegment.fulfilled.match(result)) {
      setSuccessMsg('Deleted in segment!');
    }
    await clearEditedValue();
  };

  // editedValueを初期化
  const clearEditedValue = async () => {
    await dispatch(
      editSegment({
        id: 0,
        segment_name: '',
      })
    );
  };

  return (
    <>
      <h3 data-testid='h3-segment'>Segment</h3>
      <span className={styles.segment__status}>{successMsg}</span>

      <div>
        {/* 入力 */}
        <input
          type='text'
          placeholder='new segment name'
          ref={inputRef}
          value={editedSegment.segment_name}
          onChange={async (e) =>
            await dispatch(
              editSegment({ ...editedSegment, segment_name: e.target.value })
            )
          }
        />
        <button
          data-testid='btn-post'
          disabled={!editedSegment.segment_name}
          onClick={() => (editedSegment.id === 0 ? onCreate() : onUpdate())}
        >
          {editedSegment.id === 0 ? 'Create' : 'Update'}
        </button>
        {/* リスト */}
        <ul>
          {segments &&
            segments.map((seg) => (
              <li key={seg.id} className={styles.segment__item}>
                <span data-testid={`list-${seg.id}`}>{seg.segment_name}</span>
                <div>
                  <button
                    data-testid={`edit-seg-${seg.id}`}
                    onClick={() => onFocusInput(seg)}
                  >
                    Edit
                  </button>
                  <button
                    data-testid={`delete-seg-${seg.id}`}
                    onClick={() => onDelete(seg.id)}
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

export default Segment;
```
