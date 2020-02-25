# mock数据

## 1. js文件格式：


    import mock from 'mockjs'

    var data = [
        {
            name: 'org/support/etl/transform.select-tm1.ktr',
            group: 'TIMEOUT',ronExpression: '0/10 * * * * ?',
            jobClass: 'org.support.etl.KettleJob',
            state: 'TIMEOUT',
            previousFireTime: '20200212215410',
            description: ''
        }, {
        name: 'org/support/etl/transform.select-tm2.ktr', group: 'TIMEOUT', cronExpression: '0/10 * * * * ?', jobClass: 'org.support.etl.KettleJob', state: 'TIMEOUT', reviousFireTime: '20200212215410', escription: ''}]

    mock.mock('/quartz/getTimeoutTriggers', options => {
        // options 里面包含了请求参数
        // options.body
        return data
    })

## 2. json格式

    mock.mock('/xxx/xxx', [{"name":"111","group":"aaa"},{"name":"222","group":"bbb"}])

