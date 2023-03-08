---
id: kgk6kslvfbknlug6nyj6rua
title: Ruby
desc: ''
updated: 1678266735023
created: 1678117578648
---
# Resources
 - [Ruby syntax checker](https://extendsclass.com/ruby-tester.html)
## Hash and JSON operations - Logstash

```
if [@metadata][json] {
  ruby {
    code => "
      a = Array.new
      jsondata = event.get('[@metadata][json]')
      jsondata.each { |key, value|
        h = Hash.new
        if key != 'information_disclosure' then
          h[:domain] = key
          value.each { |inner_key, inner_value|
            if inner_key == 'present' then
              c = Array.new
              inner_value.each { |inner_inner_key, inner_inner_value|
                b = Hash.new
                b[:header] = inner_inner_key
                b[:content] = inner_inner_value
                c.append(b)
              }
              h[:present] = c
            elsif inner_key == 'missing' then
              h[:missing] = value['missing']
            end
          } 
          a.append(h)
        end
      }
      event.set('shcheck', a)"
  }
}
```

## Hash and JSON operations - Ruby script

```
jsond = '<json_data>'

jsondata = JSON.parse(jsond)

a = Array.new
jsondata.each { |key, value|
    h = Hash.new
    if key != 'information_disclosure' then
        h[:domain] = key
        if value.key?('present') then
            value.each { |inner_key, inner_value|
                inner_value.each { |inner_inner_key, inner_inner_value|
                    puts "#{inner_inner_key} #{inner_inner_value}"
                    h[inner_inner_key] = inner_inner_value
                }
            }
        end
        h[:missing] = value['missing'].to_s
        a.append(h)
    end
}
```
