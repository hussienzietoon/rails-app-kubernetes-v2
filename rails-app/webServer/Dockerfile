FROM ruby:3.1

WORKDIR /app

COPY Gemfile Gemfile.lock* ./
RUN bundle install

# نسخ باقي الملفات
COPY . .

EXPOSE 8081

CMD ["ruby", "webServer.rb"]
