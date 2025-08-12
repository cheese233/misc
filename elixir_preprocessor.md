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

freopen = file

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

defmodule ToHex do
  def to_hex(num) when is_integer(num) do
    num
    |> Integer.to_string(16)
    |> String.downcase()
  end
end

freq
|> Enum.each(fn {word, idx} ->
  IO.puts(freopen, "#define _#{ToHex.to_hex(idx)}(x) #{word} x")
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

    IO.write(freopen, "_#{ToHex.to_hex(v)}(")
    gen(freq, words, freopen, idx + 1)
    IO.write(freopen, ")")
  end
end

MainQuoteGenerater.do_gen(freq, words, freopen)

IO.puts(freopen, "")

```

---
