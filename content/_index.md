+++

title = "Context-Generic Programming"

insert_anchor_links = "heading"

sort_by = "weight"

+++

# Announcement

We're excited to announce the release of [`cgp` v0.6.1](/blog/v0-6-1-release/), which brings several quality-of-life improvements to Context-Generic Programming in Rust. This release focuses on making CGP more accessible to developers new to the paradigm while also providing better debugging and verification tools for complex provider setups.

[Read the announcement blog post](/blog/v0-6-1-release/) to find out more.

---

# Quick Introduction

Context-Generic Programming (CGP) is a modular programming paradigm that enables you to bypass the **coherence restrictions** in Rust traits, allowing for **overlapping** and **orphan** implementations of any CGP trait.

You can adapt almost any existing Rust trait to use CGP today by applying the `#[cgp_component]` macro to the trait definition. After this annotation, you can write **named** implementations of the trait using `#[cgp_impl]`, which can be defined without being constrained by the coherence rules. You can then selectively enable and reuse the named implementation for your type using the `delegate_components!` macro.

For instance, we can, in principle, annotate the standard library’s [`Hash`](https://doc.rust-lang.org/std/hash/trait.Hash.html) trait with `#[cgp_component]` like this:

```rust
#[cgp_component(HashProvider)]
pub trait Hash { ... }
```

This change does not affect existing code that uses or implements `Hash`, but it allows for new, potentially overlapping implementations, such as one that works for any type that also implements `Display`:

```rust
#[cgp_impl(HashWithDisplay)]
impl<T: Display> HashProvider for T { ... }
```

You can then apply and reuse this implementation on any type by using the `delegate_components!` macro:

```rust
pub struct MyData { ... }
impl Display for MyData { ... }

delegate_components! {
    MyData {
        HashProviderComponent: HashWithDisplay,
    }
}
```

In this example, `MyData` implements the `Hash` trait by using `delegate_components!` to delegate its implementation to the `HashWithDisplay` provider, identified by the key `HashProviderComponent`. Because `MyData` already implements `Display`, the `Hash` trait is now automatically implemented through CGP via this delegation.

---

# Current Status

As of 2025, it is important to note that CGP remains in its **formative, early stages** of active development. While the potential is genuinely promising, it still has several inevitable rough edges, particularly in key areas such as comprehensive documentation, mature tooling, streamlined debugging techniques, robust community support, and overall ecosystem maturity.

Consequently, embracing CGP for your mission-critical, serious projects comes with inherent challenges, and you should be advised to proceed **at your own discretion and risk**. The primary risk you face is not strictly technical, but rather stems from the current limited support available when you inevitably encounter difficulties while learning or attempting to apply CGP in production.

At this pioneering stage, CGP is an ideal fit for [**early adopters and enthusiastic potential contributors**](/contribute/)  who are eager to experiment with the paradigm and actively help to shape its powerful future.

---

# Getting Started

Even though the CGP project is officially still less than one year old, some of the initial documentation and resources you might find have already become slightly outdated, or they have been gracefully superseded by more intuitive and modern patterns. Nevertheless, this section is an attempt to provide you with the very best and most current guidance on how you can effectively begin to learn more about CGP.

## Blog Posts

The most accurate and up-to-date resources concerning CGP are currently available in the form of our [blog posts](/blog). Specifically, we recommend that the blog posts starting from the [**v0.6.0 release onward**](/blog/v0-6-0-release/)  will give you a significantly more concise and modern explanation of what CGP is all about.

## Hello World Tutorial

The hands-on [Hello World Tutorial](/tutorials/hello) provides a high-level and clear walkthrough of various fundamental CGP features by using a familiar, simple hello-world style example.

## Book


If your preference is to rigorously understand CGP from its **first principles**, without relying on the specific implementations within the [`cgp` crate](https://github.com/contextgeneric/cgp), your best approach is to dive into our comprehensive book, [Context-Generic Programming Patterns](https://patterns.contextgeneric.dev/). This resource provides a deep, thorough guide to understanding the complex inner workings of CGP.

Please note that this book has not been updated for a while, and you might consider skipping the book entirely if your primary goal is to start using CGP quickly with the absolute minimal learning curve!

## Resources

Make sure to check out the dedicated [Resources](/resources) page, which contains more supplementary materials and various learning tools to help you get up to speed with CGP.
