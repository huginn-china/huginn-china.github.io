---
title: 创建一个新的 Agent
layout: docs
permalink: /docs/创建一个新的Agent.html
prevPage: network-image-node.html
nextPage: map-node.html
---

*请注意：Huginn API 一直在改进，因此一些无用的 Agent 或将被放弃。我们非常希望您能将您的使用方法以及 API 应该更改什么告诉我们。查看 [#60](https://github.com/cantino/huginn/issues/60) 和 [#293](https://github.com/cantino/huginn/issues/293)。*

Huginn Agent 能创建和接受事件，并且能按照预定的安排在特定的时间或时间间隔运行代码。创建一个 Agent 并不难，你可以很轻松的从一个已存在的 Agent 创建一个子 Agent。

Agent 保存在 `app/models/agents`，RSpec 保存在 `spec/models/agents`。

# 描述

使用 `description` 类方法描述你的 Agent。例如：

    description <<-MD
      The WeatherAgent creates an event for the following day's weather at `zipcode`.

      You must setup an API key for Wunderground in order to use this Agent.
    MD

# 选项

Agent 是用户使用 JSON 结构进行配置的，`options`(选项)非常容易使用，同时还可以使用 [Liquid](https://github.com/cantino/huginn/wiki/Formatting-Events-using-Liquid) 模板引擎进行更改。你应该定义一个 `default_options` 方法，该方法用来定义默认配置，另外，你还应该定义一个 `validate_options` 方法，该方法会验证 `options` 的内容，确保一些必填选项已经填写，通常情况下，你是不希望用户更改这些必填选项的，下面是一个具体的例子：

    def default_options
      { 'zipcode' => '94103' }
    end

    def validate_options
      errors.add(:base， 'zipcode is required') unless options['zipcode'].present?
    end

# 时间表

Agent 可以被设定在特定的时间或一定的时间间隔运行，当设定的时间被触发，Agent 内的 `check` 方法将会被调用。如果你的 Agent 是可调度的，需要定义 `default_schedule` 方法定义默认值，否则会调用 `cannot_be_scheduled!` 方法。可用的设定时间如下：`every_1m`， `every_2m`， `every_5m`， `every_10m`， `every_30m`， `every_1h`， `every_2h`， `every_5h`， `every_12h`， `every_1d`， `every_2d`， `every_7d`， `midnight`， `1am`， `2am`， `3am`， `4am`， `5am`， `6am`， `7am`， `8am`， `9am`， `10am`， `11am`， `noon`， `1pm`， `2pm`， `3pm`， `4pm`， `5pm`， `6pm`， `7pm`， `8pm`， `9pm`， `10pm`， `11pm`。

    default_schedule "8pm"

    def check
      wunderground.forecast_for(interpolated['zipcode'])['forecast']['simpleforecast']['forecastday'].each do |day|
        if is_tomorrow?(day)
          create_event :payload => day.merge('zipcode' => interpolated['zipcode'])
        end
      end
    end

如果你的Agent创建了事件，比如像 [WeatherAgent](https://github.com/cantino/huginn/blob/master/app/models/agents/weather_agent.rb) 这样，你应该使用 `event_description` 类方法描述这些事件包含哪些数据。

    event_description <<-MD
      Events look like this:

          {
            'zipcode' => 12345，
            ...
            'maxhumidity' => 93，
            'minhumidity' => 63
          }
    MD

# 接受事件

如果你的 Agent 能接受事件，定义 `receive` 方法，该方法接受事件的 array，否则调用 `cannot_receive_events!` 注释掉它。

# 创建事件

在代码中，你的 Agent 可以通过 `create_event :payload => { ... }` 创建事件，否则调用 `cannot_create_events!` 注释掉它。

# 内存

Agent 拥有内存池，可以用在时间间隔内或接受事件之间维持状态。它会被自动加载并保存，可通过 `memory` 获得。

# 日志

你的 Agent 应该创建日志，尤其是错误发生的时候。通过调用 `log` 或 `error` 打印日志信息，还可以使用 `:outbound_event` 或 `:inbound_event` 监测事件有关的日志信息。

# 正在工作吗?

最好将 Agent 实例的运行状态反馈给用户。你需要定义 `working?` 方法，当一切正常的时候，该方法返回 true。下面的例子中，Agent 创建事件并有一个 `expected_update_period_in_days` 选项：

    def working?
      event_created_within?(interpolated['expected_update_period_in_days']) && !recent_error_logs?
    end

当然，你也可以写一些特殊的代码在 `working?` 方法中。

# UI

Agent 的 UI 文件保存在 `app/views/agents/agent_views/<agent name>/_show.html.erb` 中。如果你需要一些服务器端的功能，你可以 POST 数据到 `handle_details_post_agent_path`，使用 `handle_details_post` 方法进行处理。查看示例： [ManualEventAgent](https://github.com/cantino/huginn/blob/master/app/models/agents/manual_event_agent.rb) 和 [details view](https://github.com/cantino/huginn/blob/master/app/views/agents/agent_views/manual_event_agent/_show.html.erb)。

# 接收web请求

通过 `receive_web_request`，你的 Agent 可以接收 web 请求。你的 Agent URL 像这样 `http://yourserver.com/users/:user_id/web_requests/:agent_id/:secret`，`:user_id` 是用户的 ID， `:agent_id` 是 Agent 的 ID，`secret` 是用户的特殊 token，通过 `receive_web_request` 验证。推荐每次调用 `receive_web_request` 时都要验证 token。例如，你的 Agent 有一个 `secret` 的选项，每次调用 `receive_web_request` 时，都将其与 params[:secret] 进行比较，从而过滤掉那些无效的请求，

你的 Agent 的 `receive_web_request` 方法应该返回一个数组 (status， MiME type) 作为响应，示例如下：

    [{ status: "success" }， 200]

或

    ["not found"， 404， 'text/plain']

这里有一个 `receive_web_request` 例子，如下所示：

```ruby
def receive_web_request(params， method， format)
  secret = params.delete('secret')
  return ["Please use POST requests only"， 401] unless method == "post"
  return ["Not Authorized"， 401] unless secret == interpolated['secret']

  # do something with params here

  ['Done!'， 200， 'text/plain']
end
```

如果你需要从请求中获得更多的参数，也可以定义 `receive_web_request` 方法如下：

```
def receive_web_request(request)
end
```

在 [WebRequestsController](https://github.com/cantino/huginn/blob/master/app/controllers/web_requests_controller.rb) 查看更多详细信息，可参考 [WebhookAgent](https://github.com/cantino/huginn/blob/master/app/models/agents/webhook_agent.rb) 和 [DataOutputAgent](https://github.com/cantino/huginn/blob/master/app/models/agents/data_output_agent.rb) 中的 `receive_web_request`。

> 本文由 [Huginn 中文网](http://huginn.cn) 翻译，已经获得项目作者授权，项目原文访问 [Creating a new agent](https://github.com/cantino/huginn/wiki/Creating-a-new-agent)

