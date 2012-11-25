# Rspec

## Ch2

### 在 project 中加入 rspec

`Gemfile`

```
group :development, :test do 
  gem 'rspec-rails'
end
```

然後跑 

* `bundle install`
* `rails generate rspec:install`

### Configuration

`spec/spec_helper.rb`

```
Dir[Rails.root.join("spec/support/**/*.rb")].each {|f| require f}
# ( require all helper files within spec/support)
….
Rspec.configure do |config|
  config.mock_with :mocha
  # allows you to change the default mocking framework
end
```

### Output

`rspec --color spec/models/zombie_spec.rb`

`rspec --color --format documentation spec/models/zombie_spec.rb`

如果不想每次都打，可以在專案的 `.rspec` 下先預先存好

```
--color
--format documentation
```

### Runing Specs

* `rspec` # 跑全部的
* `rspec spec/modles/` 跑整個目錄
* `rspec spec/models/zombie_spec.rb` 跑單個檔案
* `rspec spec/models/zombie_spec.rb:4` 跑最近一行的 test example


### Model spec

`spec/model/zombie_spec.rb`

```
require 'spec_helper'
describe Zombie do
  it 'is invalid without a name' do
    zombie = Zombie.new
    zombie.should_not be_valid # predicate matcher
  end
end
```

`app/models/zombie.rb`

```
class Zombie < ActiveRcord::Base
  validates :name, :presence => true
end
```

### Matchers: match

`spec/modles/zombie_spec.rb`

```
describe Zombie do
  it "has a name that matches 'Ash Clone`" do
    zombie = Zombie.new(:name => "Ash Clone 1")
    zombie.name.should match(/Ash Clone \d/)
  end
```

### Matchers: match

`spec/modles/zombie_spec.rb`

```
describe Zombie do
  it "include tweets" do
    tweet1 = Tweet.new(:status => "Uuuuuuhhhhh")
    tweet2 = Tweet.new(:status => "Arrrgggg")
    zombie = Zombie.new(:name => "Ash", :tweets => [tweet1,tweet2])
    zombie.tweets.should include(tweet1)
    zombie.tweets.should include(tweet2)
    # matching on an array,       # is this Tweet inside tweets ?
  end
```

### Matchers: have

`spec/modles/zombie_spec.rb`

```
describe Zombie do
  it "starts with two weapons" do
    zombie = Zombie.new(:name => "Ash")
    # zombie.weapons.count.should == 2
    zombie.should have(2).weapons
  end
```


還可以使用

* have(n)
* have_at_least(n)
* have_at_most(n)



### Matchers: change

`spec/modles/zombie_spec.rb`

```
describe Zombie do
  it 'changes  the number of Zombies' do
    zombiee = Zombie.new(:name => "Ash")
    expect { zombie.save}.to change { Zombie.count }.by(1)
      # runs before and after expect        # results are compared
  end
```

可以使用

* by(n)
* from(n)
* to(n)
* from(1).to(5)


### raise_error

`spec/modles/zombie_spec.rb`

```
describe Zombie do
  it "raise an aerror if saved without a name" do
    zombie = Zombie.new
    expect { zombie.save! }.to rais_error(
      ActiveRcord::RecordInvalid
    end
    # optionally pass in an exception
  end
```

* to 
* not_to
* to_not


### More matchers

* respond_to(<method_name>)
* be_within(<range>).of(<expected>)
* exist
* satisfy{ <block> }
* be_kind_of(<class>)
* be_an_instance_of(<class>)


@zombie.should respond_to(hungry?)
@width.should be_within(0..1).of(33.3)
@zombie.should exsit
@zombie.should satisfy { |zombie| zombie.hungry? }
@hungry_zombie.should be_kind_of(Zombie)
@status.should be_an_instance_of(String)

