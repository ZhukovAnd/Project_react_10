---
title: 'Условный рендеринг'
date: '17.10.2023'
---

React позволяет разделить логику на независимые компоненты. Эти компоненты можно показывать или прятать в зависимости от текущего состояния.

Условный рендеринг в React работает так же, как условные выражения работают в JavaScript. Бывает нужно объяснить React, как состояние влияет на то, какие компоненты требуется скрыть, а какие — отрендерить, и как именно. В таких ситуациях используйте условный оператор JavaScript или выражения подобные if.

Рассмотрим два компонента:

function UserGreeting(props) {
  return <h1>С возвращением!</h1>;
}

function GuestGreeting(props) {
  return <h1>Войдите, пожалуйста.</h1>;
}
Можно создать компонент Greeting, который отражает один из этих компонентов в зависимости от того, выполнен ли вход на сайт:

function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}

const root = ReactDOM.createRoot(document.getElementById('root')); 
// Попробуйте заменить на isLoggedIn={true}:
root.render(<Greeting isLoggedIn={false} />);
Посмотреть на CodePen

В этом примере рендерится различное приветствие в зависимости от значения пропа isLoggedIn.

Переменные-элементы
Элементы React можно сохранять в переменных. Это может быть удобно, когда какое-то условие определяет, надо ли рендерить одну часть компонента или нет, а другая часть компонента остаётся неизменной.

Рассмотрим ещё два компонента, представляющих кнопки Войти (Login) и Выйти (Logout).

function LoginButton(props) {
  return (
    <button onClick={props.onClick}>
      Войти
    </button>
  );
}

function LogoutButton(props) {
  return (
    <button onClick={props.onClick}>
      Выйти
    </button>
  );
}
В следующем примере мы создадим компонент с состоянием и назовём его LoginControl.

Он будет рендерить либо <LoginButton />, либо <LogoutButton /> в зависимости от текущего состояния. А ещё он будет всегда рендерить <Greeting /> из предыдущего примера.

class LoginControl extends React.Component {
  constructor(props) {
    super(props);
    this.handleLoginClick = this.handleLoginClick.bind(this);
    this.handleLogoutClick = this.handleLogoutClick.bind(this);
    this.state = {isLoggedIn: false};
  }

  handleLoginClick() {
    this.setState({isLoggedIn: true});
  }

  handleLogoutClick() {
    this.setState({isLoggedIn: false});
  }

  render() {
    const isLoggedIn = this.state.isLoggedIn;
    let button;
    if (isLoggedIn) {
      button = <LogoutButton onClick={this.handleLogoutClick} />;
    } else {
      button = <LoginButton onClick={this.handleLoginClick} />;
    }

    return (
      <div>
        <Greeting isLoggedIn={isLoggedIn} />
        {button}
      </div>
    );
  }
}

const root = ReactDOM.createRoot(document.getElementById('root')); 
root.render(<LoginControl />);
Посмотреть на CodePen

Нет ничего плохого в том, чтобы объявить переменную и условно рендерить компонент if-выражением. Но иногда хочется синтаксис покороче. Давайте посмотрим на несколько других способов писать условия прямо в JSX.

Встроенные условия if с логическим оператором &&
Вы можете внедрить любое выражение в JSX, заключив его в фигурные скобки. Это правило распространяется и на логический оператор && языка JavaScript, которым можно удобно вставить элемент в зависимости от условия:

function Mailbox(props) {
  const unreadMessages = props.unreadMessages;
  return (
    <div>
      <h1>Здравствуйте!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          У вас {unreadMessages.length} непрочитанных сообщений.
        </h2>
      }
    </div>
  );
}

const messages = ['React', 'Re: React', 'Re:Re: React'];

const root = ReactDOM.createRoot(document.getElementById('root')); 
root.render(<Mailbox unreadMessages={messages} />);
Посмотреть на CodePen

Приведённый выше вариант работает корректно, потому что в JavaScript-выражение true && expression всегда вычисляется как expression, а выражение false && expression — как false.

То есть, если условие истинно (true), то элемент, идущий непосредственно за &&, будет передан на вывод. Если же оно ложно (false), то React проигнорирует и пропустит его.

Обратите внимание, что ложное выражение, как ожидается, пропустит элемент после &&, но при этом выведет результат этого выражения. В примере ниже метод render вернёт <div>0</div>.

render() {
  const count = 0;
  return (
    <div>
      {count && <h1>Количество сообщений: {count}</h1>}
    </div>
  );
}
Встроенные условия if-else с тернарным оператором
Есть ещё один способ писать условия прямо в JSX. Вы можете воспользоваться тернарным оператором condition ? true : false.

Вот как этот метод можно использовать, чтобы отрендерить кусочек текста.

render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      Пользователь <b>{isLoggedIn ? 'сейчас' : 'не'}</b> на сайте.
    </div>
  );
}
Этот метод можно использовать и с выражениями покрупнее, но это может сделать код менее очевидным:

render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      {isLoggedIn
        ? <LogoutButton onClick={this.handleLogoutClick} />
        : <LoginButton onClick={this.handleLoginClick} />
      }
    </div>
  );
}
Как в JavaScript, так и в React выбор синтаксиса зависит от ваших предпочтений и принятого в команде стиля. Не забывайте, что если какое-то условие выглядит очень сложным, возможно пришло время извлечь часть кода в отдельный компонент.

Предотвращение рендеринга компонента
В редких случаях может потребоваться позволить компоненту спрятать себя, хотя он уже и отрендерен другим компонентом. Чтобы этого добиться, верните null вместо того, что обычно возвращается на рендеринг.

Например, будет ли содержимое <WarningBanner /> отрендерено, зависит от значения пропа под именем warn. Если значение false, компонент ничего не рендерит:

function WarningBanner(props) {
  if (!props.warn) {
    return null;
  }

  return (
    <div className="warning">
      Предупреждение!
    </div>
  );
}

class Page extends React.Component {
  constructor(props) {
    super(props);
    this.state = {showWarning: true};
    this.handleToggleClick = this.handleToggleClick.bind(this);
  }

  handleToggleClick() {
    this.setState(state => ({
      showWarning: !state.showWarning
    }));
  }

  render() {
    return (
      <div>
        <WarningBanner warn={this.state.showWarning} />
        <button onClick={this.handleToggleClick}>
          {this.state.showWarning ? 'Спрятать' : 'Показать'}
        </button>
      </div>
    );
  }
}

const root = ReactDOM.createRoot(document.getElementById('root')); 
root.render(<Page />);
Посмотреть на CodePen

Сам факт возврата null из метода render компонента никак не влияет на срабатывание методов жизненного цикла компонента. Например, componentDidUpdate будет всё равно вызван.



