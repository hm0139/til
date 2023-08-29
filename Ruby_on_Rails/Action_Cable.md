# Action Cable概要
ActionCableとはクライアントとサーバが双方向に通信できる技術。
リアルタイムチャットなどで使用する。

## オリジナルアプリに組み込む
オリジナルアプリphoto diorama(仮)を作成しているがそのアプリの機能の一部にチャット機能がある。
しかし現状そのチャット機能はリアルタイムではなく、Ajaxを使用して非同期でメッセージを投稿している。
投稿自体は非同期だが相手からメッセージが投稿された場合、自分側にはそれが反映されないと言う問題がある。
そこでActionCableと言う技術を使用しリアルタイムチャットを実現する。

## 組み込む前にチャットアプリを作成
まだActionCableについての理解が足りないので、オリジナルアプリに組み込む前に簡単なチャットアプリを作成してそこで色々試した。

## 作ってみる
メッセージを保存するためのモデル(Message)はcontentカラムを持ったものにする

ActionCableを使用するには次のコマンドをうつ
```
% rails g channel チャンネル名
```

ここではroomとして作成する
```
% rails g channel rooms
```

そうするとrooms_channel.rbとrooms_channel.jsが作成されるので主にこの二つを編集していく
他にも色々作成されるが、一旦置いておく

rooms_channel.rbを以下のように編集する
```ruby
class RoomsChannel < ApplicationCable::Channel
  def subscribed
    stream_from "rooms_channel"
  end

  def unsubscribed
    # Any cleanup needed when channel is unsubscribed
  end

  def post(data)
    Message.create!({content: data["message"]})
  end
end
```

rooms_channel.jsを以下のように編集する
```JavaScript
import consumer from "channels/consumer"

  const app = consumer.subscriptions.create("RoomsChannel", {
    connected() {
      // Called when the subscription is ready for use on the server
    },

    disconnected() {
      // Called when the subscription has been terminated by the server
    },

    received(data) {
      const messageContainer = document.getElementById("message-container");
      messageContainer.insertAdjacentHTML("beforeend", data["message");
    },

    post(message){
      return this.perform("post", {message: message});
    }
  });
```
