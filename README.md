## Project Status
![Generic badge](https://img.shields.io/badge/build-passing-green.svg) <br/>
[Check out the actual website!](https://beegramin9.github.io/React-NetNinja-FramerMotion/) try!

## Overview
![Pizza](https://user-images.githubusercontent.com/58083434/130402775-9caaa1d4-54e7-44ff-8c4e-b07cf9940318.gif)

## Technology Stack
<img src="https://img.shields.io/badge/React-61DAFB?style=flat-square&logo=React&logoColor=white"/></a>&nbsp;
<img src="https://img.shields.io/badge/Framer-df0eb1?style=flat-square&logo=Framer&logoColor=white"/>&nbsp;
<img src="https://img.shields.io/badge/StyledComponents-DB7093?style=flat-square&logo=Styled-Components&logoColor=white"/></a>

## Outline
&nbsp; Framer-motion is a production-ready animation library for React. This app is a simple Pizza recipe app where users can select pizza base, topping and get a customized pizza. The pizza base and topping options are React states that are in custody with useState. Every routes has some cool effects & animations generated by Framer motion, including motion Link transition. 
<br/><br/>
&nbsp; ReactJS 진영의 animation 라이브러리인 Framer-motion을 사용했습니다. Link transition, hover & tapping effect, transition effect들이 곳곳에 녹아있는 웹 서비스입니다. 대부분의 effect가 css3의 transform과 keyframes로 구현 가능하지만, framer motion은 ReactJS의 components와도 interactive하게 작용하여 개발을 용이하게 해줍니다. 또한 Link 간의 transition이 smooth하게 이뤄지도록 만들어졌습니다.

## Main Feature Code
- React Routing & Key States <br/>
> (/src/App.js) <br/>
> pizza state의 구성요소인 base, topping state를 useState로 관리하고 리액트 라우팅에 컴포넌트로 전달합니다.
```js
function App() {
  //* get & update information about current route location
  //* 대표적으로 pathname = slug, search = querystring 정도가 있음
  const location = useLocation(); 
  const [pizza, setPizza] = useState({ base: "", toppings: [] });
  const [showModal, setShowModal] = useState(false);

  const addBase = (base) => {
    setPizza({ ...pizza, base }) // ...pizza는 base를 제외한 요소, toppings: [...] 입니다.
  }
  
  const addTopping = (topping) => {
    let newToppings;
    // if 기존 toppings에 없는 새로운 topping =>
    if(!pizza.toppings.includes(topping)){
      // 단순하게 기존 Array에 더해주기만 하면 된다
      newToppings = [...pizza.toppings, topping]; 
    // if 기존 toppings에 속한 topping =>
    } else {
      // 원래 있던 topping을 한번 더 눌러서 deactivate, 즉 제거하는 것이므로
      // 해당 deactivated된 topping을 제외, filter해서 새로운 array를 만든다
      newToppings = pizza.toppings.filter(item => item !== topping);
    }
    setPizza({ ...pizza, toppings: newToppings }); // ...pizza는 toppings를 제외한 요소, base: "..."입니다.
  }
  
  // Base, Topping, Order는 import되는 페이지 컴포넌트입니다.
  return (
    <>
      <Header />
      <Modal showModal={showModal} />
      {/* AnimatePresence로 감싸면 모든 Component의 exit를 관리할 수 있습니다.
      즉, Component끼리의 exit이 서로 겹치지 않고 queue와 같이 선출선입의 순서를 보장합니다. */} 
      <AnimatePresence exitBeforeEnter onExitComplete={() => setShowModal(false)}> 
        <Switch location={location} key={location.key}>
          <Route path="/base">
            <Base addBase={addBase} pizza={pizza} />
          </Route>
          <Route path="/toppings">
            <Toppings addTopping={addTopping} pizza={pizza} />
          </Route>
          <Route path="/order">
            <Order pizza={pizza} setShowModal={setShowModal} />
          </Route>
          <Route path="/">
            <Home />
          </Route>
        </Switch>
      </AnimatePresence>
    </>
  );
}

export default App;
```
- Variants <br/>
> (src/components/Base.js 등 모든 Page Component에 적용) <br/>
> variants는 motion component에 전달할 수 있는 props를 변수화한 것입니다. hover, tap, link transition의 애니메이션을 줄 수 있습니다. 
```js
const buttonVariants = {
  hover: {
    scale: 1.1,
    textShadow: "0px 0px 8px rgb(255,255,255)",
    boxShadow: "0px 0px 8px rgb(255,255,255)",
    transition: {
      duration: 0.3,
      yoyo: 5 // 반복횟수, Infinity도 가능합니다.
    }
  },
  tap: {
    scale: 1.1,
    textShadow: "0px 0px 8px rgb(255,255,255)",
    boxShadow: "0px 0px 8px rgb(255,255,255)",
    transition: {
      duration: 0.3,
      yoyo: 5
    }
  }
}

return (
    ...
    <motion.button
    variants={buttonVariants}
    whileHover="hover"
    whileTap="tap">
        Create Your Pizza
    </motion.button>
        ...
       )
```


- Smooth Link Transition <br/>
> (src/components/Base.js 등 모든 Page Component에 적용) <br/>
> react-router-dom의 <Link> 컴포넌트는 이미 새로고침 없이 링크 트랜지션을 가능하게 합니다. 하지만 Framer-motion은 트랜지션 마다 페이지가 역동적으로 좌로 빠지고 우로 들어오는 등 애니메이션 효과를 매우 쉽게 줄 수 있습니다.
  
 ```js
 const containerVariants = {
  hidden: { 
    opacity: 0, 
    x: '100vw' // x축, pageX와 같이 시작점을 스크린의 가장 왼쪽으로 잡습니다. 즉 시작점이 100vw인 것은 시작점일 땐 보이지 않는 것입니다.
  },
  visible: { 
    opacity: 1, 
    x: 0, // 애니메이션이 끝나면 원래 자리 0으로 돌아옵니다. 
    transition: { type: 'spring', delay: 0.5 } // type은 css transform의 ease-in-out, cubic-bezier 와 같이 속도를 조절하는 기능입니다.
  },
  exit: {
    x: "-100vh",
    transition: { ease: 'easeInOut' }
  }
};
  
 const Base = ({ addBase, pizza }) => {
  const bases = ['Classic', 'Thin & Crispy', 'Thick Crust'];

  return (
    <motion.div className="base container" // 주로 모든 페이지에 적용되는 Layout 컴포넌트마다 링크 트랜지션을 주면 수월합니다.
      variants={containerVariants}
      initial="hidden" // 애니메이션 시작 전, css로 치면 transition | keyframes 시작 전입니다.
      animate="visible" // 애니메이션 완료 후, css로 치면 transition | keyframes 가 다 완료된 상태입니다.
      exit="exit" // 애니메이션 퇴장, 해당 컴포넌트가 링크 트랜지션 등으로 퇴장할 때 줄 수 있는 애니메이션입니다.
    >
      // 해당 페이지를 구성하는 JSX...
    </motion.div>
  )
}

export default Base;
 ```
