+++
date = 2020-08-12T20:50:00+03:00
title = "Why blogging any way?!"
+++

It's been a while since I have enough time to explore and share on technical stuff, recently I thought of de-misfitting some exotics tech-buzz-words I hear - So I set my first goal which to understand genetic algorithms.

I followed this nice [intro to genetic algorithms](https://towardsdatascience.com/introduction-to-genetic-algorithms-including-example-code-e396e98d8bf3) by Vijini Mallawaarachchi.

First I have implemented a rough implementation of 0/1 genes in Rust, to understand the mechanics. It was pretty strait forward. 

One of the key elements is being able to randomize properly, which was a good opportunity to look into the rand crate. I learned this nice pattern of generating random stuff using different distributions as needed. For example my Gene is either 0 or 1, so having random Gene is implemented like this:

```rust
use rand::{
    distributions::{Distribution, Uniform},
    Rng,
};

#[derive(Debug)]
enum Gene {
    Zero,
    One,
}

// THIS IS WHERE THE RANDOMIZATION HAPPENS
impl Gene {
    fn random<R: Rng + ?Sized>(rng: &mut R) -> Gene {
        let uniform = Uniform::new_inclusive(0, 1);
        match uniform.sample(rng) {
            0 => Gene::Zero,
            _ => Gene::One,
        }
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test] 
    fn test_random() {
        let mut rng = rand::thread_rng();
        let gene = Gene::random(&mut rng);
        println!("{:?}", gene);
    }
}
```

Next I had to implement mutation which should change a Gene with lower probability. Not that hard:

```rust
use rand::Bernoulli;

impl Gene {
    fn mutate<R: Rng + ?Sized>(&mut self, rng: &mut R) {
        let bernoulli = Bernoulli::new(0.1).unwrap();
        if bernoulli.sample(rng) {
            *self = match self {
                Gene::Zero => Gene::One,
                Gene::One => Gene::Zero,
            };
        }
    }

#[cfg(test)]
mod tests {
    use super::*;

    #[test] 
    fn test_mutate() {
        let mut rng = rand::thread_rng();
        let mut gene = Gene::random(&mut rng);
        println!("{:?}", gene);
        gene.mutate(&mut rng);
        println!("{:?}", gene);
    }
}
```

Nice one Bernoulli!!

The source code can be found at [here](https://github.com/vim-zz/genes-just-want-to-have-fun) tagged `2020-08-12`