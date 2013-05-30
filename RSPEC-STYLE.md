# RSpec Style Guide

* Use `I18n.t` instead of hardcoded translations
* Use `let` instead of instance variables
* Don't nest too deep - maximum 3 levels of `describe`/`context`
* Prefer `spec/support/*` over `spec/spec_helper.rb`
* Prefer eq to == in RSpec

## Factories

* Use factory_girl and not fixtures
* Prefer `spec/factories/*.rb` over `spec/factories.rb`

## Model Specs

* Use `subject(:object)`
* Name outer `describe` blocks after the method under test.
  Use `.method` for class methods and `#method` for instance methods
* Don't create too many database objects, use `FactoryGirl.build` where possible
* Use `context` for contexts, they should typically start with "when" or "with"

Example:

```ruby
describe User
  subject(:user) { build :user }

  describe "#authenticate" do
    before do
      user.password = "pass123"
    end

    context "when password is correct and user is not locked" do
      it "should return user" do
        user.is_locked = false
        user.authenticate("pass123").should eq user
      end
    end

    context "when password is incorrect and user is not locked" do
      it "should return nil" do
        user.is_locked = false
        user.authenticate("1337").should eq nil
      end
    end

    context "when password is correct and user is locked" do
      it "should return nil" do
        user.is_locked = true
        user.authenticate("pass123").should eq nil
      end
    end
  end
end
```

Output:

```
User
  #authenticate
    when password is correct and consumer is not locked
      should return consumer
    when password is incorrect and user is not locked
      should return nil
    when password is correct and consumer is locked
      should return nil
```

## Feature Specs

* Use `spec/features` to store feature specs
* Use `feature`/`scenario` over `describe`
* Use only one `feature` block per feature spec file
* Use scenario titles that describe the success and failure paths
* Use `let!` for objects that are required for all scenarios

Example:

```ruby
feature 'Sessions' do
  let!(:user) { create :user }

  scenario 'User signs in using valid username and password' do
    User.should_receive(:authenticate).
      with(user.username, 'password').
      and_return(user)

    visit new_session_path

    fill_in 'user_username', with: user.username
    fill_in 'user_password', with: 'password'

    click_button I18n.t('helpers.submit.sign_in')

    page.should have_content user.full_name
    page.should have_link t('nav.sign_out')
  end
end
```

Output:

```
Sessions
  User signs in using valid username and password
```

## Recommended gems

* factory_girl
* capybara
* capybara-screenshot
* poltergeist
* database_cleaner
* webmock or vcr

## More information

* [Better Specs](http://betterspecs.org/)
* []