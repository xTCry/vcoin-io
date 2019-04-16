# vcoin-io

<p>
    <a href="https://www.npmjs.com/package/vcoin-io"><img src="https://img.shields.io/npm/v/vcoin-io.svg?style=flat-square" alt="NPM version"></a>
    <!--<a href="https://www.npmjs.com/package/vcoin-io"><img src="https://img.shields.io/npm/dt/vcoin-io.svg?style=flat-square" alt="NPM downloads"></a>-->
</p>

Работа с API VK Coin

## Установка
> **[Node.js](https://nodejs.org/) 8.0.0 или новее**

### NPM
```shell
npm i vcoin-io
```

## Примеры использования
Для начала использования нужно создать в своей папке исполняемый файл, пусть это будет `index.js`

Теперь его нужно открыть и импортировать библиотеку:
```js
const VCOINIO = require('vcoin-io');

const vkcoin = new VCOINIO({
    key: "BN^!uqga7g2i#ga38w7Gur^gb3^47eg&",
    userId: 101,
    token: "qqqqqqqqqTOKENqVKqqqqqqqqqqq"
});
```

|Опция|Тип|Описание|
|-|-|-|
|key|String|Ключ для взаимодействия с API|
|userId|Number|Ваш ID ВК|
|token|String|Ваш [токен](https://vkhost.github.io) ВК|

# Методы
getTransactionList - Получает список ваших транзакций

```js
async function run() {
    const result = await vkcoin.getTransactionList(tx);
    
    console.log(result);
}

run().catch(console.error);
```

|Параметр|Тип|Описание|
|-|-|-|
|tx|Array<Number>|Массив айди переводов для получения ИЛИ [1] - последняя 1000 транзакций, [2] - 100|
#
sendPayment - Делает перевод другому пользователю

```js
async function run() {
    const result = await vkcoin.sendPayment(toId, amount);
    
    console.log(result);
}

run().catch(console.error);
```

|Параметр|Тип|Описание|
|-|-|-|
|toId|Number|ID получателя|
|amount|Number|Сумма перевода|
#
getLink - Получает ссылку для перевода

```js
function run() {
    const link = vkcoin.getLink(amount, fixation);
    
    console.log(link);
}

run().catch(console.error);
```

|Параметр|Тип|Описание|
|-|-|-|
|amount|Number|Сумма перевода|
|fixation|Boolean|Фиксированная сумма или нет|
#
formatCoins - Делает получаемое из API значение коинов читабельным. Например, приходит значение 1234567890. Этот метод сделает значение таким: 1 234 567,890

Это можно использовать в паре с другим методом:
```js
async function run() {
    const trans = await vkcoin.getTransactionList([2]);

    const fixTrans = trans.response.map((tran) => {
        tran.amount = vkcoin.formatCoins(tran.amount);

        return tran;
    });

    console.log(fixTrans);
}

run().catch(console.error);
```
|Параметр|Тип|Описание|
|-|-|-|
|coins|Number|Входящее значение коинов|
#
getBalance - Получает баланс по айди пользователей

getMyBalance - Получает баланс текущего пользователя

```js
async function run() {
    const balances = await vkcoin.getBalance([1, 100, 236908027]);
    const myBalance = await vkcoin.getMyBalance();

    console.log({ balances, myBalance });
}

run().catch(console.error);
```

Среди этих методов аргумент принимает только getBalance:


|Параметр|Тип|Описание|
|-|-|-|
|userIds|Array<Number>|Массив айди пользователей|

# Updates
**updates** - Позволяет "прослушивать" события в VK Coin. Пока что я реализовал перехват входящего платежа, но вскоре придумаю что-нибудь ещё. И да, впервые работаю с сокетами :)

### Запуск
Для запуска прослушивания есть специальный метод startPolling. Он является асинхронным, поэтому запускать его нужно в асинхронной функции:

```js
async function run() {
    await vkcoin.updates.startPolling();
    
    /* Тут ваши действия со слушателем */
}

run().catch(console.error);
```

Метод не принимает аргументов
#
updates.onTransfer - Перехватывает входящие платежи, принимает один аргумент

```js
async function run() {
    await vkcoin.updates.startPolling();

    vkcoin.updates.onTransfer((event) => {
        console.log(event);
    });
}

run().catch(console.error);
```

event - Объект, который хранит в себе информацию о платеже:

|Параметр|Тип|Описание|
|-|-|-|
|amount|Number|Количество коинов, которые поступили на счёт|
|fromId|Number|ID плательщика|
|id|Number|ID платежа|

# Ссылки
* [Разработчик vcoinapi](https://github.com/fakemancat)
* [Кто-то еще](https://github.com/xtcry)
* [Dev Беседа VK](https://vk.me/join/AJQ1d_JeTA/o0GfCxwihS_6E)
