## ListContainer に DragAndDrop を組み込む

- DragDropContext
	- DragAndDrop を実装するルート部分に設置する
	- onDragEnd
- Droppable
	- droppableId 
	- type 
	- direction
	- 子要素
		- droppableProps 
		- innerRef
		- placeholder 

### app/(platform)/(dashboard)/board/\[boardId]/\_components/list-container.tsx

```tsx
'use client';

//...
import { DragDropContext, Droppable, DragUpdate } from '@hello-pangea/dnd';
//...

export default function ListContainer({ boardId, data }: ListContainerProps) {
  //...

  const onDragEnd = (result: DragUpdate) => {
    //...
  }

  //...
  return (
    <DragDropContext onDragEnd={onDragEnd}>
      <Droppable droppableId='lists' type='list' direction='horizontal'>
        {(provided) => (
          <ol
            {...provided.droppableProps}
            ref={provided.innerRef}
            className='flex gap-x-3 h-full'
          >
            {orderedData.map((list, index) => {
              return <ListItem key={list.id} index={index} data={list} />;
            })}
            {provided.placeholder}
            <ListForm />
            <div className='flex-shrink-0 w-1' />
          </ol>
        )}
      </Droppable>
    </DragDropContext>
  );
}
```

## ListItem に DragAndDrop を組み込む 

- Draggable
	- draggableId 
	- index
	- 子要素 
		- draggableProps 
		- innerRef 
		- dragHandleProps
- Droppable 
	- droppableId 
	- type 
	- 子要素 
		- placeholder

### app/(platform)/(dashboard)/board/\[boardId]/\_components/list-item.tsx

```tsx
'use client';

//...
import { Draggable, Droppable } from '@hello-pangea/dnd';
//...

export default function ListItem({ index, data }: ListItemProps) {
  //...

  return (
    <Draggable draggableId={data.id} index={index}>
      {(provided) => (
        <li
          {...provided.draggableProps}
          ref={provided.innerRef}
          className='shrink-0 h-full w-[272px] select-none'
        >
          <div
            {...provided.dragHandleProps}
            className='w-full rounded-md bg-[#f1f2f4] shadow-md pb-2'
          >
            <ListHeader onAddCard={enableEditing} data={data} />
            <Droppable droppableId={data.id} type='card'>
              {(provided) => (
                <ol
                  {...provided.droppableProps}
                  ref={provided.innerRef}
                  className={cn(
                    'mx-1 px-1 py-0.5 flex flex-col gap-y-2',
                    data.cards.length > 0 ? 'mt-2' : 'mt-0'
                  )}
                >
                  {data.cards.map((card, index) => (
                    <CardItem key={card.id} index={index} data={card} />
                  ))}
                  {provided.placeholder}
                </ol>
              )}
            </Droppable>
            <CardForm
              listId={data.id}
              isEditing={isEditing}
              enableEditing={enableEditing}
              disableEditing={disableEditing}
            />
          </div>
        </li>
      )}
    </Draggable>
  );
}
```

## CardItem に DragAndDropを組み込む 

- 基本は ListContainer と同じ

### app/(platform)/(dashboard)/board/\[boardId]/\_components/card-item.tsx

```tsx
'use client';

//...
import { Draggable } from '@hello-pangea/dnd';
//...

export default function CardItem({ index, data }: CardItemProps) {
  return (
    <Draggable draggableId={data.id} index={index}>
      {(provided) => (
        <div
          {...provided.draggableProps}
          {...provided.dragHandleProps}
          ref={provided.innerRef}
          role='button'
          className='truncate border-2 border-transparent hover:border-black py-2 px-3 text-sm bg-white rounded-md shadow-sm'
        >
          {data.title}
        </div>
      )}
    </Draggable>
  );
}
```