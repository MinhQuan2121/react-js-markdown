# Hook gắn vào components

```javascript
import {
  useState,
  useEffect,
  useLayoutEffect,
  useRef,
  useCallback,
  useMemo,
  useReducer,
  useContext,
  useImperativeHandle,
  useDebugValue,
} from 'react';

// Chưa dùng Hooks, chỉ là UI component
function ComponentA() {
  return <h1>Haven't used hooks yet</h1>;
}

function ComponentB() {
  // useState
  const [state, setState] = useState(initState);
  // ...
}
```

1. Chỉ dùng cho function component
2. Component đơn giản và trở nên dễ hiểu
   - Không bị chia logic ra như methods trong lifecycle của Class Component
   - Không cần sử dụng "this"
3. Sử dụng Hooks khi nào?
   - Dự án mới => Hooks
   - Dự án cũ
     - Component mới => Function component + Hooks
     - Component cũ => Giữ nguyên, có thời gian tối ưu sau
   - Logic nghiệp vụ cần sử dụng các tính chất OOP => Class component
4. Người mới nên bắt đầu với Function hay Class component => Function component
5. Có kết hợp Function và Class component không?
   - Được

```jsx
function Item({ children }) {
  return <li>{children}</li>;
}

class List extends React.Component {
  render() {
    return (
      <ul>
        <Item>Html, CSS</Item>
        <Item>Javascript</Item>
        <Item>React JS</Item>
      </ul>
    );
  }
}
```

# Tất cả các hook

# useState (trạng thái của dữ liệu)

VD: người đó đổi từ cảm xúc này sang cảm xúc khác
Nguyễn văn A -> Nguyễn Văn B

## Dùng khi nào?

Khi muốn dữ liệu thay đổi thì giao diện tự động
dược cập nhật (render lại dữ liệu).

## Cách dùng

```jsx
import { useState } from 'react';

function Component() {
  const [state, setState] = useState(initState);

  ...
}
```

## Lưu ý

- Component được re-render sau khi `setState`
- Initial state chỉ dùng cho lần đầu
- Set state với callback
- Initial state với callback
- Set state là thay thế state bằng giá trị mới

## Note

EX1:
Problem:

```jsx
const handleIncrease = () => {
  setCounter(counter + 1);
  setCounter(counter + 1);
  setCounter(counter + 1);
};
```

- Giải thích:
  VD: counter = 4 <br>
  4 + 1 output 5 tiếp tục với function kế tiếp <br>
  4 + 1 output 5 tiếp tục với function kế tiếp <br>
  4 + 1 output 5 xong rồi re-render lại function <br>
  ra đáp án cuối là 5

Solution: Set state với callback

```jsx
const handleIncrease = () => {
  setCounter((prevCounter) => prevCounter + 1);
  setCounter((prevCounter) => prevCounter + 1);
  setCounter((prevCounter) => prevCounter + 1);
};
```

- Trong đoạn code của bạn, khi gọi liên tiếp `setCounter` với callback `(prevCounter) => prevCounter + 1`, React sẽ sử dụng <b>giá trị mới nhất của state</b> trong mỗi lần cập nhật, thay vì dùng giá trị ban đầu như khi bạn chỉ truyền `counter + 1` trực tiếp. Điều này giúp đảm bảo mỗi lần cập nhật sử dụng giá trị hiện tại của `counter`, không phải giá trị cũ.

## Nguyên lý hoạt động:

1. Lần gọi `setCounter` đầu tiên

   - `prevCounter = 4` (giá trị hiện tại của `counter`)
   - Thực hiện `prevCounter + 1` => `5`
   - State tạm thời được cập nhật, nhưng chưa có re-render ngay lập tức.

2. Lần gọi `setCounter` thứ hai:

   - Giá trị `prevCounter` sẽ là giá trị cập nhật mới nhất, tức là `5`.
   - Thực hiện `prevCounter + 1` => `6`
   - Tiếp tục, giá trị state được tạm thời lưu là `6`.

3. Lần gọi `setCounter` thứ ba:
   - Giá trị `prevCounter` lại được cập nhật lên giá trị mới nhất, tức là 6.
   - Thực hiện `prevCounter + 1` => `7`
   - Sau lần này, state tạm thời là `7`.

EX2: Problem 2: Initial state với callback

```jsx
const [counter, setCounter] = useState(() => {
  const total = orders.reduce((total, curl) => total + curl);

  console.log(total);
  return total;
});
```

EX3: Problem 3: Set state là thay thế state bằng giá trị mới

```jsx
function App() {
  const [info, setInfo] = useState({
    name: 'Nguyen Van A',
    age: 18,
    address: 'Ha Noi, VN',
  });

  const handleUpdate = () => {
    setInfo({
      ...info,
      bio: 'Hello World!',
    });
  };

  return (
    <div className="App">
      <h1>{JSON.stringify(info)}</h1>
      <button onClick={handleUpdate}>Update</button>
    </div>
  );
}
```

Ở đây có thể dùng Callback

```jsx
const handleUpdate = () => {
  setInfo((prev) => ({
    ...prev,
    bio: 'Hello World!',
  }));
};
```

# Two-way binding

## EX1: Random gift

```jsx
import { useState } from 'react';

const gifts = [
  'CPU i9',
  'RAM 32GB RGB',
  'RGB keyboard',
  'Logitech g102',
  'Headphone',
  '100$',
  'Mousepad',
  'Microphone',
  'Bad luck',
];

function App() {
  const [gift, setGift] = useState();

  const randomGift = () => {
    const index = Math.floor(Math.random() * gifts.length);
    setGift(gifts[index]);
  };

  return (
    <div>
      <h1>{gift || 'No gift'}</h1>
      <button onClick={randomGift}>Get gift</button>
    </div>
  );
}

export default App;
```

<b>Cách hoạt động: <br></b>
Khi ứng dụng được chạy lần đầu, `gift` là `undefined`, nên `h1` hiển thị "No gift".
Khi người dùng nhấn vào nút "Get gift", hàm `randomGift` được gọi, chọn ngẫu nhiên một phần tử từ mảng `gifts` và cập nhật `gift`. Sau đó, giao diện sẽ re-render và hiển thị quà tặng đã chọn.

## One-way binding

```jsx
import { useState } from 'react';

function App() {
  const [name, setName] = useState();

  console.log(name);

  return (
    <div>
      <input onChange={(e) => setName(e.target.value)} />
      {/* <button onClick={() => setName("Nguyen Van B")}></button> */}
    </div>
  );
}

export default App;
```

## Two-way binding

```jsx
import { useState } from 'react';

function App() {
  const [name, setName] = useState();

  console.log(name);

  return (
    <div>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <button onClick={() => setName('Nguyen Van B')}>Change</button>
    </div>
  );
}

export default App;
```

```jsx
import { useState } from 'react';

function App() {
  const [name, setName] = useState();
  const [email, setEmail] = useState();

  const handleSubmit = () => {
    console.log({
      name,
      email,
    });
  };

  return (
    <div>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <input value={email} onChange={(e) => setEmail(e.target.value)} />
      <button onClick={handleSubmit}>Register</button>
    </div>
  );
}

export default App;
```

```jsx
import { useState } from 'react';

// Response from API
const courses = [
  {
    id: 1,
    name: 'HTML, CSS',
  },

  {
    id: 2,
    name: 'Javascript',
  },

  {
    id: 3,
    name: 'React JS',
  },
];

function App() {
  const [checked, setChecked] = useState(2);

  console.log(checked);

  const handleSubmit = () => {
    console.log({ id: checked });
  };

  return (
    <div>
      {courses.map((course) => (
        <div key={course.id}>
          <input
            type="radio"
            checked={checked === course.id}
            onChange={() => setChecked(course.id)}
          />
          {course.name}
        </div>
      ))}
      <button onClick={handleSubmit}>Submit</button>
    </div>
  );
}

export default App;
```

```jsx
import { useState } from 'react';

// Response from API
const courses = [
  {
    id: 1,
    name: 'HTML, CSS',
  },

  {
    id: 2,
    name: 'Javascript',
  },

  {
    id: 3,
    name: 'React JS',
  },
];

function App() {
  const [checked, setChecked] = useState([]);

  console.log(checked);

  const handleSubmit = () => {
    console.log({ ids: checked });
  };

  const handleCheck = (id) => {
    setChecked((prev) => {
      const isChecked = checked.includes(id);
      if (isChecked) {
        return checked.filter((item) => item !== id);
      } else {
        return [...prev, id];
      }
    });
  };

  return (
    <div>
      {courses.map((course) => (
        <div key={course.id}>
          <input
            type="checkbox"
            checked={checked.includes(course.id)}
            onChange={() => handleCheck(course.id)}
          />
          {course.name}
        </div>
      ))}
      <button onClick={handleSubmit}>Submit</button>
    </div>
  );
}

export default App;
```

## Todolist (using useState())

```jsx
import { useState } from 'react';

function App() {
  const storageJobs = JSON.parse(localStorage.getItem('jobs'));

  const [job, setJob] = useState('');
  const [jobs, setJobs] = useState(storageJobs ?? []);

  const handleSubmit = () => {
    setJobs((prev) => {
      const newJobs = [...prev, job];

      // Save to local storage
      const jsonJobs = JSON.stringify(newJobs);
      localStorage.setItem('jobs', jsonJobs);

      return [...prev, job];
    });
    setJob('');
  };

  console.log(job);

  return (
    <div>
      <input value={job} onChange={(e) => setJob(e.target.value)} />
      <button onClick={handleSubmit}>Add</button>
      <ul>
        {jobs.map((job, index) => (
          <li key={index}>{job}</li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```

```jsx
import { useState } from 'react';

function App() {
  const [job, setJob] = useState('');
  const [jobs, setJobs] = useState(() => {
    const storageJobs = JSON.parse(localStorage.getItem('jobs'));
    console.log(storageJobs);
    return storageJobs;
  });

  const handleSubmit = () => {
    setJobs((prev) => {
      const newJobs = [...prev, job];

      // Save to local storage
      const jsonJobs = JSON.stringify(newJobs);
      localStorage.setItem('jobs', jsonJobs);

      return [...prev, job];
    });
    setJob('');
  };

  return (
    <div>
      <input value={job} onChange={(e) => setJob(e.target.value)} />
      <button onClick={handleSubmit}>Add</button>
      <ul>
        {jobs.map((job, index) => (
          <li key={index}>{job}</li>
        ))}
      </ul>
    </div>
  );
}

export default App;
```

# Mounted & Unmounted

```jsx
import React from 'react';
import Content from './components/Content.js';
import { useState } from 'react';

function App() {
  const [show, setShow] = useState(false);

  return (
    <div>
      <button onClick={() => setShow(!show)}>Toggle</button>
      {show && <Content />}
    </div>
  );
}

export default App;
```

Nếu show = `true` thì Content xuất hiện, nếu show = `false` thì Content không xuất hiện.

```jsx
function Content() {
  return <h1>Hi anh em F8</h1>;
}

export default Content;
```

# (side effect) useEffect(callback, [deps]) . deps không bắt buộc

- Khi component `re-render` lại thì đoạn code trong `useEffect` sẽ được thực thi

1. useEffect(callback)

   ```jsx
   function Content() {
    useEffect(() => {
     //
   });
     return ()
   }
   ```

- Gọi callback mỗi khi component re-render
- Gọi callback sau khi component thêm element vào DOM

2. useEffect(callback, [])

- Chỉ gọi callback một lần sau khi component mounted

3. useEffect(callback, [deps])

- Callback sẽ được gọi lại mỗi khi deps thay đổi - Cleanup function luôn được gọi trước khi unmounted
- Cleanup function luôn được gọi trước khi component unmounted
- Cleanup function luôn được gọi trước khi callback được gọi (trừ lần mounted)

```jsx
// Example
useEffect(() => {
  const handleScroll = () => {
    if (window.scrollY >= 200) {
      setShowGotoTop(true);
    } else {
      setShowGotoTop(false);
    }
  };
  window.addEventListener('scroll', handleScroll);

  // Cleanup function
  return () => {};
}, []);
```

  <br>

Note (chung cho 3 trường hợp):

- Callback luôn được gọi sau khi component mounted

Cần nắm được:

- Events: Add / remove event listener
- Observer pattern: Subscribe / unsubscribe
- Closure
- Timers: setInterval, setTimeout, clearInterval, clearTimeout
- useState
- Mounted / Unmounted
- ===
- Call API

## 1. Update DOM - F8 blog title

Khi Nào useEffect Chạy:

<b>Lần Đầu Tiên Render:</b> Khi component được render lần đầu tiên, callback function trong `useEffect` sẽ được gọi. <br>
<b>Khi Có Sự Thay Đổi Về Dependencies:</b> Nếu bạn cung cấp một mảng dependencies, `useEffect` sẽ được gọi lại mỗi khi một trong các giá trị trong mảng đó thay đổi. <br>
<b>Không Có Dependencies:</b> Nếu bạn không cung cấp mảng dependencies, `useEffect` sẽ chạy lại mỗi lần component re-render. <br>
<b>Mảng Rỗng:</b>> Nếu bạn cung cấp một mảng rỗng (`[]`), `useEffect` chỉ chạy một lần sau lần render đầu tiên (tương tự như `componentDidMount` trong class components). <br>

## 2. Call API

```jsx
import { useEffect, useState } from 'react';

function Content() {
  const [title, setTitle] = useState('');
  const [posts, setPosts] = useState([]);

  useEffect(() => {
    fetch('https://jsonplaceholder.typicode.com/posts')
      .then((res) => res.json())
      .then((posts) => {
        setPosts(posts);
      });
  }, []);

  return (
    <div>
      <input value={title} onChange={(e) => setTitle(e.target.value)} />
      <ul>
        {posts.map((post) => (
          <li key={post.id}>{post.title}</li>
        ))}
      </ul>
    </div>
  );
}

export default Content;
```

```jsx
useEffect(() => {
  fetch('https://jsonplaceholder.typicode.com/posts')
    .then((res) => res.json())
    .then((posts) => {
      setPosts(posts);
    });
}, []);
```

- Ở đây `useEffect(callback, [])` phải sử dụng với `[]` nếu không muốn `setPosts(posts)` lặp đi lặp lại vô hạn (làm yếu performance), nên ta sẽ dùng `[]` rỗng để nó chạy một lần duy nhất khi `component mounted`.

EX:

```jsx
import { useEffect, useState } from 'react';

const tabs = ['posts', 'comments', 'albums'];

function Content() {
  const [title, setTitle] = useState('');
  const [posts, setPosts] = useState([]);
  const [type, setType] = useState('posts');

  useEffect(() => {
    fetch(`https://jsonplaceholder.typicode.com/${type}`)
      .then((res) => res.json())
      .then((posts) => {
        setPosts(posts);
      });
  }, [type]);

  return (
    <div>
      {tabs.map((tab) => (
        <button
          key={tab}
          style={type === tab ? { color: '#fff', backgroundColor: '#333' } : {}}
          onClick={() => setType(tab)}
        >
          {tab}
        </button>
      ))}

      <input value={title} onChange={(e) => setTitle(e.target.value)} />
      <ul>
        {posts.map((post) => (
          <li key={post.id}>{post.title || post.name}</li>
        ))}
      </ul>
    </div>
  );
}

export default Content;
```

- Ở đây, `callback` thay đổi khi `deps (type)` thay đổi (khi chọn tabs).
  VÌ file `JSON` của `comments` không có `title` nên ta có thể dùng `||`

## 3. Listen DOM Events <br> 2 Example: <br> - Scroll <br> - Resize

Ex 1: Scroll

```jsx
import { useEffect, useState } from 'react';

const tabs = ['posts', 'comments', 'albums'];

function Content() {
  const [showGoToTop, setShowGotoTop] = useState(false);

  useEffect(() => {
    const handleScroll = () => {
      if (window.scrollY >= 200) {
        setShowGotoTop(true);
      } else {
        setShowGotoTop(false);
      }
    };
    window.addEventListener('scroll', handleScroll);
    // Cleanup function
    return () => {
      window.removeEventListener('scroll', handleScroll);
    };
  }, []);

  return (
    <div>
      {showGoToTop && (
        <button style={{ position: 'fixed', right: 20, bottom: 20 }}>
          Go to Top
        </button>
      )}
    </div>
  );
}

export default Content;
```

Chú ý: Cleanup function ở đây sử dụng để sửa lỗi `memory leak`.

```jsx
// Cleanup function
return () => {
  window.removeEventListener('scroll', handleScroll);
};
```

## 4. Cleanup (useEffect() with DOM events) <br> - Remove listener / Unsubscribe <br> - Clear timer

EX 1: Remove listener / Unsubscribe

```jsx
import { useEffect, useState } from 'react';

const tabs = ['posts', 'comments', 'albums'];

function Content() {
  const [width, setWidth] = useState(window.innerHeight);

  useEffect(() => {
    const handleResize = () => {
      setWidth(window.innerWidth);
    };
    window.addEventListener('resize', handleResize);

    return () => {
      window.removeEventListener('resize', handleResize);
    };
  }, []);

  return (
    <div>
      <h1>{width}</h1>
    </div>
  );
}

export default Content;
```

EX 2: Clear timer

<b>setInterval</b>

```jsx
import { useEffect, useState } from 'react';

function Content() {
  const [countdown, setCountdown] = useState(180);

  useEffect(() => {
    const timerID = setInterval(() => {
      setCountdown((prev) => prev - 1);
    }, 1000);

    return () => clearInterval(timerID);
  }, []);

  return (
    <div>
      <h1>{countdown}</h1>
    </div>
  );
}

export default Content;
```

<b>setTimeout</b>

```jsx
import { useEffect, useState } from 'react';

function Content() {
  const [countdown, setCountdown] = useState(180);

  useEffect(() => {
    setTimeout(() => {
      setCountdown(countdown - 1);
    }, 1000);
  }, []);

  return (
    <div>
      <h1>{countdown}</h1>
    </div>
  );
}

export default Content;
```

EX 3: Cleanup function luôn được gọi trước khi callback được gọi (trừ lần mounted)

```jsx
import { useEffect, useState } from 'react';

function Content() {
  const [count, setCount] = useState(1);

  useEffect(() => {
    console.log(`Mounted or Re-render ${count}`);

    // Cleanup func

    return () => {
      console.log(`Cleanup ${count}`);
    };
  }, [count]);

  return (
    <div>
      <h1>{count}</h1>
      <button onClick={() => setCount(count + 1)}>Click me!</button>
    </div>
  );
}

export default Content;
```

```jsx
import { useEffect, useState } from 'react';

function Content() {
  const [avatar, setAvatar] = useState();

  useEffect(() => {
    return () => avatar && URL.revokeObjectURL(avatar.preview);
  }, [avatar]);

  const handlePreviewAvatar = (e) => {
    const file = e.target.files[0];

    file.preview = URL.createObjectURL(file);

    setAvatar(file);
  };

  return (
    <div>
      <input type="file" onChange={handlePreviewAvatar} />
      {avatar && <img src={avatar.preview} alt="" width="80%" />}
    </div>
  );
}

export default Content;
```

# useLayoutEffect

Sự khác nhau của useEffect và useLayoutEffect

<b>useEffect</b><br>

1. Cập nhật lại state
2. Cập nhật DOM (mutated)
3. Render lại UI
4. Gọi cleanup nếu deps thay đổi
5. Gọi useEffect callback

<b>useLayoutEffect</b><br>

1. Cập nhật lại state
2. Cập nhật DOM (mutated)
3. Render lại UI
4. Gọi cleanup nếu deps thay đổi (sync)
5. Render lại UI

# useRef

- `useRef` là một hook trong React giúp bạn tạo ra một đối tượng có thể lưu trữ giá trị mà không bị thay đổi trong suốt vòng đời của component. Khác với `useState`, khi giá trị của `useRef` thay đổi, nó không gây ra việc render lại component. `useRef` thường được sử dụng để lưu trữ tham chiếu tới một phần tử DOM hoặc giữ giá trị qua các lần render mà không cần render lại.

- Khác biệt giữa `useRef` và `useState`:
  - `useState`: Khi giá trị thay đổi, component sẽ re-render.
  - `useRef`: Thay đổi giá trị không làm re-render component.

code

```jsx
import React, { useState, useEffect, useRef } from 'react';

export default function Content() {
  const [name, setName] = useState('');
  const renderCount = useRef(0);

  useEffect(() => {
    renderCount.current++;
  });

  return (
    <div>
      <input value={name} onChange={(e) => setName(e.target.value)} />
      <div>My name is {name}</div>
      <div>I rendered {renderCount.current} times</div>
    </div>
  );
}
```

```jsx
import React, { useState, useEffect, useRef } from 'react';

export default function Content() {
  const [name, setName] = useState('');
  const inputRef = useRef();

  function focus() {
    inputRef.current.focus();
  }

  return (
    <div>
      <input
        ref={inputRef}
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <div>My name is {name}</div>
      <button onClick={focus}>Focus</button>
    </div>
  );
}
```

- `ref` không chỉ dùng cho `input`

Lưu ý:

```jsx
import React, { useState, useEffect, useRef } from 'react';

export default function Content() {
  const [name, setName] = useState('');
  const inputRef = useRef();

  function focus() {
    inputRef.current.focus();
    inputRef.current.value = 'Some value';
  }

  return (
    <div>
      <input
        ref={inputRef}
        value={name}
        onChange={(e) => setName(e.target.value)}
      />
      <div>My name is {name}</div>
      <button onClick={focus}>Focus</button>
    </div>
  );
}
```

`    inputRef.current.value = 'Some value';`
không làm thay đổi `<div>My name is {name}</div>`

# Tổng kết các giai đoạn lifecycle của các hook:

- <b>Mount (Khi component tạo):</b> `useState` khởi tạo giá trị, `useEffect` chạy lần đầu.
- <B>Update (Khi component cập nhật):</B> `useEffect` chạy lại nếu dependencies thay đổi, state cập nhật làm re-render component.
- <b>Unmount (Khi component gỡ bỏ):</b> Hàm cleanup trong `useEffect` được gọi để dọn dẹp.
  <br> Nếu bạn cần cụ thể hơn về từng hook hoặc có ví dụ rõ ràng hơn, cứ cho tôi biết nhé!

# React.memo() HOC (Higher Order Component)

1. Ứng dụng cần thiết (không sử dụng `React.memo()`)

`App.js`

```jsx
import React, { useState } from 'react';
import Content from './components/Content.js';

function App() {
  const [count, setCount] = useState(0);

  const handleAdd = () => {
    setCount((prev) => prev + 1);
  };

  return (
    <div>
      <Content count={count} />
      <h1>{count}</h1>
      <button onClick={handleAdd}>Click me!</button>
    </div>
  );
}

export default App;
```

`  Content.js`

```jsx
import React from 'react';

function Content({ count }) {
  console.log('Re-render');

  return (
    <div>
      <h1>You counted {count}</h1>
    </div>
  );
}

export default Content;
```

- Ứng dụng không cần thiết

`App.js`

```jsx
import React, { useState } from 'react';
import Content from './components/Content.js';

function App() {
  const [count, setCount] = useState(0);

  const handleAdd = () => {
    setCount((prev) => prev + 1);
  };

  return (
    <div>
      <Content />
      <h1>{count}</h1>
      <button onClick={handleAdd}>Click me!</button>
    </div>
  );
}

export default App;
```

`Content.js`

```jsx
import React from 'react';
import { memo } from 'react';

function Content() {
  console.log('Re-render');

  return (
    <div>
      <h1>You counted</h1>
    </div>
  );
}

export default memo(Content);
```

- Vì vậy ở đây dùng `memo()` để khi có sự thay đổi ở `App.js` thì `<Content />` không thay đổi.
- Thế nhưng `ví dụ đầu tiên` ở trên vẫn dùng được nếu có `memo()`

# useCallback()

<b>App.js</b>

```jsx
import React, { useState, useCallback } from 'react';
import ChildComponent from './ChildComponent';

export default function App() {
  const [count, setCount] = useState(0);

  // Ghi nhớ hàm handleIncrease, chỉ tạo lại khi `count` thay đổi
  const handleIncrease = useCallback(() => {
    setCount((prev) => prev + 1);
  }, [count]);

  return (
    <div>
      <ChildComponent onIncrease={handleIncrease} />
      <h1>Count: {count}</h1>
    </div>
  );
}
```

<b>ChildComponent.js</b>

```jsx
import React from 'react';

function ChildComponent({ onIncrease }) {
  console.log('ChildComponent re-rendered');

  return (
    <div>
      <button onClick={onIncrease}>Increase Count</button>
    </div>
  );
}

export default React.memo(ChildComponent);
```

<b>Kết quả:</b>

- Khi `count` thay đổi, chỉ có `App` re-render và hàm `handleIncrease` giữ nguyên, không làm `ChildComponent` re-render lại.
- Nếu không có `useCallback`, `handleIncrease` sẽ được tạo lại mỗi lần `App` re-render, dẫn đến việc `ChildComponent` re-render ngay cả khi không cần thiết. <br>

<b>Khi nào dùng `useCallback`?
</b>

- Component con nhận hàm từ Component cha và không muốn Component con re-render lại trừ khi thực sự cần thiết.
- Tránh lạm dụng: `useCallback` có thể khiến code phức tạp hơn nếu sử dụng không đúng cách. Hãy chỉ dùng khi Component con bị re-render quá nhiều và bạn nhận thấy vấn đề về hiệu năng.
