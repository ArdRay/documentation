---
id: kgk6kslvfbknlug6nyj6rua
title: Ruby
desc: ''
updated: 1678117634386
created: 1678117578648
---

## Hash and JSON operations

```
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