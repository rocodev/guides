# Rspec

## Ch 1

### 安裝 rspec

`gem install rspec`

### 在 project 中加入 rspec

`rspect --init`

### 結構

`spec/lib/zombie_spec.rb` (<name_of_spec>_spec.rb)

```
require "spec_helper"
describe "A Zombie" do  
  # your examples (tests) go here
end
```

====

`spec/lib/zombie_spec.rb` 

```
require "spec_helper"
describe "A Zombie" do  
  it "is named Ash" # name of the example ( examples are declared using the "it" method)
end
```

====

`spec/lib/zombie_spec.rb` 
```
require "spec_helper"
describe Zombie do  # class we want to test
  it "is named Ash" # name of the example ( examples are declared using the "it" method)
end
```

### Expectations

`spec/lib/zombie_spec.rb` 

```
require "spec_helper"
describe Zombie do  
  it "is named Ash" do 
    zombie = Zombie.new
    zombie.name.should == 'Ash'  # (expectations)
  end
end
```

 
在 Test::Unit 裡面，等於 `assert_equal 'Ash', zmobie.name`

`lib/zombie.rb`

```
class Zombie
  attr_accessor :name
  
  def initialize
    @name = 'Ash'
  end
end 
```

### Another Expectations

`spec/lib/zombie_spec.rb` 

```
describe Zombie do
  …
  it "has no brains" do
    zombie = Zombie.new
    zombie.brains.should < 1
    # `<` => matcher
    # `should` => modifier
  end
end
```


`lib/zombie.rb`

```
class Zombie
  attr_accessor :name, brains
  
  def initialize
    @name = 'Ash'
    @brains = 0
  end
end 
```


### Other matchers

* zombie.name.should == 'Ash'
* zombie.alive.should == false  || zombie.alive.should be_false
* zombie.rotting.shoild ==  true || zombie.rotting.should be_true
* zombie.height.should > 5 || zombie.height.should be < 1
* zombie.height.should >= 5
* zombie.height.should < 5
* zombie.height.should_not == 5 


### Testing a predicate

`lib/zombie.rb`

```
class Zombie
  def hungry? # (predicate)
    true
  end
end 
```

`spec/lib/zombie_spec.rb` 

```
  describe Zombie do 
    it 'is hungry' do
      zombie = Zombie.new
      # zombie.hungry?.should == true
      # zombie.hungry?.should be_true
      zombie.should be_hungry # ( predicated matcher)
    end
  end
```


### to mark as pending

```
it "is named Ash"
```

只寫 it 而不寫內容就可以暫時做成 pending example

```
xit "is named Ash" do
end
```

或者把 it 變成 xit 也可以造成 pending

```
it "is named Ash" do
  pending
end
```

在裡面加入 `pending` 也可以造成 pending

