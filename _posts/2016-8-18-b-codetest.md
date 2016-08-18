---
layout: post
title: code test
---

{% highlight ruby %}
def foo
  puts 'foo'
end
{% endhighlight %}

```Ruby
# Ruby 입니다.
=begin
  구석에서 구석까지 가는 루트는 총 세가지 입니다.
  1. root(a**2 + (b+c)**2)
  2. root(b**2 + (a+c)**2)
  3. root(c**2 + (a+b)**2)

  만일 a > b >= c (왜냐하면 a = b = c 가 될수 없으므로) 라고 가정하면

  위의 루트중에서 가능한 경우의 수는 최소임을 감안할때 1번 밖에 없습니다.
  즉, a**2 + j**2(j=b+c) = 제곱수 가 되는 순서쌍 a b c를 찾아주면 됩니다.
  이는 피타고라스의 수를 찾는 것과 같으므로, 일단 a를 1부터 증가시켜 최초로 count가 1000000을 넘는 a를 찾으면 됩니다.

  여기에서 고정된 a 에 대한 j를 bruteforce방식으로 찾은 뒤,
  j와 a의 관계에 의한 식으로 count를 증가시켜줍니다.

  n**2 알고리즘이 탄생합니다.(원래는 n**3알고리즘이었는데 훨씬 빠르네요.)

  1sec..
=end

class Problem86
  def self.find_ans
    count = 0

    a = 1
    loop do
      count += get_count(a)
      return a if count > 1000000
      a += 1
    end
  end

  def self.get_count(a)
    count = 0

    (2*a).downto(2) do |j|
      exp = Math.sqrt((a**2 + j**2))
      if exp.floor === exp.ceil
        count += (j<=a) ? j/2 : a - j/2 + ((j % 2 === 0) ? 1 : 0)
      end
    end

    count
  end
end

start = Time.now
p Problem86.find_ans
p Time.now - start

```
