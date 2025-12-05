```
            const url = 'https://cmsnew.the9.com/api/news/{newsid}';
            fetch(url, {{
                method: 'PUT',
                headers: {{ 'Content-Type': 'application/json'}},
                body: JSON.stringify({{data:{{publishedAt:new Date()}}}})
            }})
            .then(resp => resp.json())
            .then(data => alert('审核通过并发布成功!'))
            .catch(error => alert('审核失败: '+error.message))
```