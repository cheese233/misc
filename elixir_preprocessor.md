---
title: A C preprocessor marco generator
---
An experiment using Elixir, generates nothing but a mess.

```elixir
words =
  IO.gets("here puts the string: ")
  |> String.trim()
  |> String.replace(~r/\n/, " ")
  |> String.split(" ")

file = File.open!("nothing.c", [:write])

freopen = file #:stdio

IO.puts(freopen, ~S<
#define MSG_HELPER(...) #__VA_ARGS__
#define MSG(x) MSG_HELPER(x)
>)

freq =
  words
  |> Enum.frequencies()
  |> Enum.map(fn {k, v} -> {v, k} end)
  |> Enum.sort()
  |> Enum.reverse()
  |> Enum.map(fn {_v, k} -> k end)
  |> Enum.with_index()

IO.puts(freopen, "#define _s(x) \\n" <> Enum.at(words, 0) <> " x")

freq
|> Enum.each(fn {word, idx} ->
  IO.puts(freopen, "#define _#{Base.encode16(<<idx>>)}(x) #{word} x")
end)

IO.puts(freopen, "#define _e() " <> Enum.at(words, -1) <> "\\n")

defmodule MainQuoteGenerater do
  def do_gen(freq, words, freopen), do: gen(freq, words, freopen, 0)

  def gen(freq, words, freopen, idx) when idx == 0 do
    IO.write(freopen, "#pragma message MSG(_s(")
    gen(freq, words, freopen, idx + 1)
    IO.write(freopen, "))")
  end

  def gen(_, words, freopen, idx) when idx == length(words) - 1 do
    IO.write(freopen, "_e()")
  end

  def gen(freq, words, freopen, idx) do
    {_, v} =
      Enum.find(freq, fn {k, _} ->
        k == Enum.at(words, idx)
      end)

    IO.write(freopen, "_#{Base.encode16(<<v>>)}(")
    gen(freq, words, freopen, idx + 1)
    IO.write(freopen, ")")
  end
end

MainQuoteGenerater.do_gen(freq, words, freopen)

IO.puts(freopen, "")

```

---
