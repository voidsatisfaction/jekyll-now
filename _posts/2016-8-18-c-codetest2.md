---
layout: post
title: whynot?
---

```ruby
# This is highlighted code
def foo
  puts 'foo'
end
```
```python
# Here is some in python
def foo():
  print 'foo'
```

# Ruby 입니다.
=begin
  ** Ruby의 내장메소드 combination을 사용하면 간단합니다.
  가능한 6자리(엄밀히 얘기하면 7자리도 될 수있는(6과 9는 같으므로))의 배열을 ary1 ary2두개로 놓고 bruteforce
=end

class Problem90
  def self.find_ans
    blocks = (0..9).to_a.combination(6).to_a
    blocks.each do |block|
      if block.include?(6) and block.include?(9)
        next
      elsif block.include?(6)
        block << 9
      elsif block.include?(9)
        block << 6
      else
        next
      end
      block.sort!
    end

    cnt = 0
    0.upto(blocks.length-1) do |i|
      i.upto(blocks.length-1) do |j|
        cnt += 1 if check(blocks[i],blocks[j])
      end
    end

    cnt
  end

  def self.check(ary1,ary2)
    std = [1,4,9,16,25,36,49,64,81]
    ary1.each{|a| ary2.each{|b| std.delete_if{|num| ((num == 10*a + b) or (num == 10*b + a)) } } }

    std.empty? ? true : false
  end
end

start = Time.now
p Problem90.find_ans
p Time.now - start
